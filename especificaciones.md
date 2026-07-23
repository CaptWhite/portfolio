# Especificaciones Tecnicas - Porfolio.dev

## 1. Resumen del Proyecto

| Campo | Valor |
|-------|-------|
| **Nombre** | porfolio-dev |
| **Version** | 0.0.1 |
| **Tipo** | Aplicacion web estatica (SSG) |
| **Arquitectura** | Pagina unica (Single Page Application) |
| **Idioma principal** | Catalan (con secciones en espanol) |
| **URL de produccion** | `https://captwhite.com/` |
| **Plantilla base** | Fork de [midudev/porfolio.dev](https://github.com/midudev/porfolio.dev) |

### Proposito

Sitio web portafolio personal para el desarrollador **Captwhite (Jordi Blanca)**, ingeniero industrial/informatico retirado, capitan de yate, y estudiante de astronomia, fisica y matematica. El sitio presenta experiencia laboral, proyectos destacados y biografia personal.

### Stack Tecnologico

| Tecnologia | Version | Funcion |
|------------|---------|---------|
| Astro | 4.4.5 | Framework de sitio estatico |
| Tailwind CSS | 3.4.1 | Framework de estilos utility-first |
| TypeScript | 5.3.3 | Tipado estatico |
| @astrojs/tailwind | 5.1.0 | Integracion Astro-Tailwind |
| @astrojs/check | 0.5.6 | Verificacion de tipos Astro |
| astro-robots-txt | 1.0.0 | Generacion automatica de robots.txt |
| @fontsource-variable/onest | 5.0.2 | Fuente autoalojada "Onest Variable" |

**Nota:** No se utiliza ningun framework de JavaScript (React, Vue, Svelte). El sitio es puramente basado en componentes Astro con JavaScript minimo del lado del cliente.

---

## 2. Arquitectura General

### Estructura de Directorios

```
porfolio.dev-main/
├── public/                          # Activos estaticos
│   ├── favicon.svg                  # Icono del sitio
│   ├── me.png                       # Foto "Sobre mi"
│   ├── midudev.webp                 # Avatar del Hero
│   ├── porfolio.webp                # Imagen Open Graph
│   ├── noise.svg                    # Textura de grano
│   └── projects/                    # Capturas de proyectos
│       ├── mee2027.webp
│       ├── astro.webp
│       ├── adventjs.webp
│       └── svgl.webp
├── src/
│   ├── components/                  # Componentes Astro reutilizables
│   │   ├── icons/                   # 16 componentes de iconos SVG
│   │   │   ├── ArrowRight.astro
│   │   │   ├── Briefcase.astro
│   │   │   ├── Code.astro
│   │   │   ├── CodeDots.astro
│   │   │   ├── ExternalLink.astro
│   │   │   ├── HeartFilled.astro
│   │   │   ├── Link.astro
│   │   │   ├── LinkedIn.astro
│   │   │   ├── Mail.astro
│   │   │   ├── Moon.astro
│   │   │   ├── NextJS.astro
│   │   │   ├── ProfileCheck.astro
│   │   │   ├── Sun.astro
│   │   │   ├── System.astro
│   │   │   ├── Tailwind.astro
│   │   │   └── UserHeart.astro
│   │   ├── AboutMe.astro
│   │   ├── Experience.astro
│   │   ├── ExperienceItem.astro
│   │   ├── Footer.astro
│   │   ├── Header.astro
│   │   ├── Hero.astro
│   │   ├── LinkButton.astro
│   │   ├── LinkInline.astro
│   │   ├── Projects.astro
│   │   ├── SectionContainer.astro
│   │   ├── SocialPill.astro
│   │   ├── ThemeToggle.astro
│   │   └── TitleSection.astro
│   ├── data/                        # Archivos de datos JSON
│   │   ├── experience.json
│   │   └── projects.json
│   ├── layouts/
│   │   └── Layout.astro             # Layout global unico
│   ├── pages/
│   │   ├── index.astro              # Pagina principal
│   │   └── components.astro         # Showcase de componentes (dev)
│   └── env.d.ts                     # Declaraciones de tipos Astro
├── astro.config.mjs                 # Configuracion de Astro
├── tailwind.config.mjs              # Configuracion de Tailwind
├── tsconfig.json                    # Configuracion de TypeScript
└── package.json                     # Dependencias y scripts
```

### Patron de Arquitectura

1. **Arquitectura de Pagina Unica:** `index.astro` renderiza todas las secciones (Hero, Experience, Projects, AboutMe) como una pagina con scroll. La navegacion usa anclajes (`#experiencia`, `#proyectos`, `#sobre-mi`).

2. **Renderizado Guiado por Datos:** Los datos de proyectos y experiencia se almacenan en archivos JSON (`src/data/`), se importan en componentes y se mapean para generar la interfaz. Esto facilita actualizaciones de contenido sin modificar codigo de componentes.

3. **Jerarquia de Componentes:**
   ```
   Layout.astro
     ├── Header.astro
     │     └── ThemeToggle.astro
     │           ├── icons/Sun.astro
     │           ├── icons/Moon.astro
     │           └── icons/System.astro
     ├── <slot /> (contenido de la pagina)
     │     ├── SectionContainer.astro (Hero)
     │     │     └── Hero.astro
     │     │           ├── SocialPill.astro
     │     │           ├── icons/LinkedIn.astro
     │     │           └── icons/Mail.astro
     │     ├── SectionContainer.astro (Experiencia)
     │     │     ├── TitleSection.astro
     │     │     └── Experience.astro
     │     │           └── ExperienceItem.astro
     │     │                 ├── LinkInline.astro
     │     │                 └── icons/ArrowRight.astro
     │     ├── SectionContainer.astro (Proyectos)
     │     │     ├── TitleSection.astro
     │     │     └── Projects.astro
     │     │           ├── LinkButton.astro
     │     │           ├── icons/ExternalLink.astro
     │     │           ├── icons/NextJS.astro
     │     │           └── icons/Tailwind.astro
     │     └── SectionContainer.astro (Sobre mi)
     │           └── AboutMe.astro
     └── Footer.astro
           └── icons/HeartFilled.astro
   ```

4. **Arquitectura Islands (Minimal):** Astro por defecto no envia JavaScript. El unico JS del lado del cliente es:
   - Logica del selector de tema (localStorage, toggling de clases)
   - IntersectionObserver del header (resaltado de enlaces de navegacion)
   - Ambos estan escritos como `<script>` dentro de componentes `.astro`

5. **Diseno Responsivo:** Utilitarios responsive de Tailwind (`sm:`, `md:`, `lg:`) en todo el sitio. El layout se adapta de movil (apilado) a escritorio (lado a lado) para experiencias y tarjetas de proyectos.

6. **Modo Oscuro:** Basado en clases con la variante `dark:` de Tailwind, gestionado por el componente `ThemeToggle` con persistencia en localStorage y deteccion de preferencias del sistema.

---

## 3. Especificaciones de Paginas

### 3.1 Pagina Principal (`src/pages/index.astro`)

La pagina principal renderiza cuatro secciones apiladas verticalmente dentro del `Layout`:

| Seccion | ID de Anclaje | Componente | Contenido |
|---------|---------------|------------|-----------|
| Hero | (sin anclaje) | `Hero.astro` | Avatar, saludo, bio, enlaces sociales |
| Experiencia | `#experiencia` | `Experience.astro` | Timeline de historial laboral |
| Proyectos | `#proyectos` | `Projects.astro` | Tarjetas de proyectos destacados |
| Sobre mi | `#sobre-mi` | `AboutMe.astro` | Biografia y foto personal |

**Estructura de renderizado:**
```astro
<Layout>
  <main id="hero">
    <SectionContainer class="py-16 md:py-36">
      <Hero />
    </SectionContainer>
  </main>
  <div class="space-y-24">
    <SectionContainer id="experiencia">
      <TitleSection>Experiencia laboral</TitleSection>
      <Experience />
    </SectionContainer>
    <SectionContainer id="proyectos">
      <TitleSection>Proyectos</TitleSection>
      <Projects />
    </SectionContainer>
    <SectionContainer id="sobre-mi">
      <TitleSection>Sobre mi</TitleSection>
      <AboutMe />
    </SectionContainer>
  </div>
</Layout>
```

### 3.2 Showcase de Componentes (`src/pages/components.astro`)

Pagina de referencia para desarrollo que muestra el sistema de diseno:

- Demuestra el componente `Badge`
- Demuestra el componente `SocialPill`
- No esta enlazada desde la navegacion principal
- Accesible en `/components`

---

## 4. Especificaciones de Componentes

### 4.1 Layout (`src/layouts/Layout.astro`)

**Funcion:** Wrapper global de pagina para todas las paginas.

**Caracteristicas:**
- Importa fuente `@fontsource-variable/onest`
- Meta tags completos: Open Graph, Twitter Cards, JSON-LD (schema Person)
- `<ViewTransitions />` de Astro para transiciones de pagina
- Background con gradiente radial (amber/orange en modo claro, mas oscuro en modo oscuro)
- Textura de grano `noise.svg` superpuesta a baja opacidad
- Estilos globales para animaciones CSS

**Estilos globales definidos:**
- `@keyframes blur`: Animacion de backdrop-blur para el header al hacer scroll
- `@keyframes fade-in-up`: Animacion de entrada con delay escalonado (`.anim-delay-1` a `.anim-delay-4`)
- `.gradient-name`: Utilidad de gradiente para el nombre del Hero (amber a orange)
- `[data-section]`: Animaciones scroll-driven usando `animation-timeline: view()` (progressive enhancement)
- Soporte para `prefers-reduced-motion`: desactiva todas las animaciones

**SEO integrado:**
- Open Graph tags (og:title, og:description, og:image, og:url)
- Twitter Card tags (twitter:card, twitter:title, etc.)
- JSON-LD con schema `Person` y enlaces sociales

### 4.2 Header (`src/components/Header.astro`)

**Funcion:** Barra de navegacion fija en la parte superior.

**Contenido:**
- 4 enlaces de navegacion: Experiencia, Projectes, Sobre mi, Contacte
- Componente `ThemeToggle`

**Comportamiento:**
- `IntersectionObserver` para resaltar el enlace de la seccion visible actualmente (texto amarillo + fondo)
- Animacion de aparicion al hacer scroll: el nav comienza transparente y se convierte en una pastilla de vidrio esmerilado (`animation-timeline: scroll()`)
- Posicion fija con `z-index` alto

### 4.3 Hero (`src/components/Hero.astro`)

**Funcion:** Seccion de introduccion/intro.

**Contenido:**
- Avatar (`midudev.webp`) con efecto de resplandor amarillo ambiental
- Saludo: "Hola, soc Captwhite" con nombre en gradiente (amber/orange)
- Texto biografico: ingeniero industrial/inf. retirado, capitan de yate, pasion por astronomia, fisica y matematica
- 2 `SocialPill`: email (`captwhite57@gmail.com`) y LinkedIn

**Iconos utilizados:** `Mail.astro`, `LinkedIn.astro`

### 4.4 Experience (`src/components/Experience.astro`)

**Funcion:** Contenedor que renderiza la lista de experiencias laborales.

**Comportamiento:**
- Importa `experience.json`
- Mapea el array para renderizar un `<ol>` con `ExperienceItem` para cada entrada
- Animaciones de entrada escalonadas con delays incrementales

### 4.5 ExperienceItem (`src/components/ExperienceItem.astro`)

**Funcion:** Entrada individual del timeline de experiencia.

**Props:**
| Prop | Tipo | Descripcion |
|------|------|-------------|
| `title` | string | Titulo del puesto |
| `company` | string | Nombre de la empresa |
| `description` | string | Descripcion del puesto |
| `link` | string (opcional) | URL externa de referencia |
| `date` | string | Rango de fechas |
| `current` | boolean | Si es el puesto actual |

**Renderizado:**
- Estilo de timeline con linea vertical a la izquierda
- Indicador de punto (con animacion de pulsacion si `current: true`)
- Titulo en amarillo, nombre de empresa, fecha, descripcion
- Enlace opcional "Saber mas" con icono de flecha

### 4.6 Projects (`src/components/Projects.astro`)

**Funcion:** Contenedor de tarjetas de proyectos.

**Definicion de Tags:**
```javascript
const TAGS = {
  NEXT: { name: "Next.js", class: "bg-black text-white", icon: NextJS },
  TAILWIND: { name: "Tailwind CSS", class: "bg-[#003159] text-white", icon: Tailwind },
}
```

**Comportamiento:**
- Mapea `projects.json` resolviendo objetos de tags
- Renderiza tarjetas con imagen responsive, efectos hover (scale, shadow, overlay gradiente)
- Titulo, badges de tags, descripcion
- Boton "Preview" que abre en nueva pestana

**Nota:** El mapa TAGS solo incluye NEXT y TAILWIND. Los tags REACT, ZUSTAND, AXIOS, FASTAPI, PYTHON, ASTROPY del `projects.json` no estan mapeados, por lo que no se muestran con iconos ni colores.

### 4.7 AboutMe (`src/components/AboutMe.astro`)

**Funcion:** Seccion de biografia personal.

**Contenido:**
- Texto biografico extenso
- Foto personal (`me.png`)

### 4.8 SectionContainer (`src/components/SectionContainer.astro`)

**Funcion:** Wrapper generico de `<section>`.

**Props:**
| Prop | Tipo | Descripcion |
|------|------|-------------|
| `class` | string (opcional) | Clases CSS adicionales |
| `id` | string (opcional) | ID para anclaje de navegacion |

**Caracteristicas:**
- Agrega atributo `data-section` (usado para animaciones scroll-triggered)
- Ancho maximo: `max-w-4xl` en pantallas grandes, `max-w-2xl` en medianas

### 4.9 TitleSection (`src/components/TitleSection.astro`)

**Funcion:** Encabezado de seccion con acento decorativo.

**Renderizado:**
- `<h2>` con contenido del slot
- Barra de acento gradiente amarillo debajo del encabezado

### 4.10 SocialPill (`src/components/SocialPill.astro`)

**Funcion:** Boton de enlace con forma de pastilla.

**Estilo:**
- Bordes redondeados, ancho completo en hover
- Soporte dark/light mode
- Usado en el Hero para enlaces sociales (email, LinkedIn)

### 4.11 LinkButton (`src/components/LinkButton.astro`)

**Funcion:** Boton de enlace rectangular.

**Estilo:**
- Borde y fondo con soporte dark/light mode
- Usado para enlaces de preview de proyectos

### 4.12 LinkInline (`src/components/LinkInline.astro`)

**Funcion:** Enlace de texto inline.

**Estilo:**
- Color amarillo de acento
- Usado dentro de ExperienceItem para enlaces "Saber mas"

### 4.13 ThemeToggle (`src/components/ThemeToggle.astro`)

**Funcion:** Selector de tema con tres modos: Light, Dark, System.

**Comportamiento:**
- Almacena preferencia en `localStorage`
- Muestra/oculta iconos Sun, Moon, System segun el tema actual
- Menu desplegable con animacion de scale-up
- Maneja la media query `prefers-color-scheme` para el modo sistema
- Persiste a traves de Astro ViewTransitions (`transition:persist`)

**Iconos utilizados:** `Sun.astro`, `Moon.astro`, `System.astro`

### 4.14 Footer (`src/components/Footer.astro`)

**Funcion:** Pie de pagina del sitio.

**Contenido:**
- Aviso de copyright con anio actual y enlace a `midu.dev`
- Enlace "Contacto" (mailto)

**Observacion:** El footer todavia referencia a "midudev", indicando que es un fork que no se ha renombrado completamente.

---

## 5. Modelos de Datos

### 5.1 Experiencia Laboral (`src/data/experience.json`)

**Estructura del array:**

| Campo | Tipo | Descripcion |
|-------|------|-------------|
| `date` | string | Rango de fechas (ej: "Sept 1981 - Jul 2021") |
| `title` | string | Titulo del puesto |
| `company` | string | Nombre de la empresa/lugar |
| `description` | string | Descripcion detallada del puesto |
| `link` | string (opcional) | URL de referencia externa |
| `current` | boolean | Si es el puesto actual |

**Entradas actuales (2):**

1. **Sept 1981 - Jul 2021:** "Tecnic de Sistemes i Xarxes" en una compania de seguros en Catalunya. Experiencia extensa en mainframe, redes, seguridad, virtualizacion y cloud computing durante 40 anos. `current: true`
2. **Ene 2022 - Presente:** "Desenvolupador web i estudiant d'astronomia, fisica i matematica". Autonomo. Enfocado en el proyecto Eddington para los eclipses solares de 2026/2027. `current: true`

### 5.2 Proyectos (`src/data/projects.json`)

**Estructura del array:**

| Campo | Tipo | Descripcion |
|-------|------|-------------|
| `title` | string | Titulo del proyecto |
| `description` | string | Descripcion del proyecto |
| `link` | string (opcional) | URL de preview en nueva pestana |
| `image` | string | Ruta relativa a la imagen en `/projects/` |
| `tags` | array de strings | Tags tecnologicos del proyecto |

**Entradas actuales (2):**

1. **"Documentacio del programari MEE2027"** - Documentacion para el Modern Eddington Experiment 2027 (proyecto cientifico participativo). Tags: NEXT, TAILWIND. Imagen: `projects/mee2027.webp`
2. **"AdventJS - Retos de programacion con JavaScript y TypeScript"** - (Nota: la descripcion describe una aplicacion de analisis de imagenes astronomicas). Tags: REACT, ZUSTAND, AXIOS, FASTAPI, PYTHON, ASTROPY. Imagen: `projects/astro.webp`

**Inconsistencia detectada:** El titulo del segundo proyecto es "AdventJS" pero la descripcion corresponde a una aplicacion de analisis de imagenes astronomicas.

---

## 6. Configuracion Tecnica

### 6.1 Configuracion de Astro (`astro.config.mjs`)

```javascript
import { defineConfig } from 'astro/config';
import tailwind from '@astrojs/tailwind';
import robotsTxt from 'astro-robots-txt';

export default defineConfig({
  integrations: [tailwind(), robotsTxt()],
  site: 'https://porfolio.dev/',
});
```

**Integraciones:**
- `@astrojs/tailwind`: Integracion de Tailwind CSS
- `astro-robots-txt`: Generacion automatica de `robots.txt`

**URL del sitio:** `https://porfolio.dev/`

### 6.2 Configuracion de Tailwind (`tailwind.config.mjs`)

```javascript
/** @type {import('tailwindcss').Config} */
export default {
  content: ['./src/**/*.{astro,html,js,jsx,md,mdx,svelte,ts,tsx,vue}'],
  theme: {
    extend: {},
  },
  darkMode: 'class',
  plugins: [],
};
```

**Caracteristicas:**
- **Dark mode:** Basado en clases (`darkMode: 'class'`), toggled por el componente ThemeToggle
- **Contenido:** Todos los archivos en `src/` con extensiones: `.astro, .html, .js, .jsx, .md, .mdx, .svelte, .ts, .tsx, .vue`
- **Extensiones del tema:** Ninguna (tema por defecto de Tailwind)
- **Plugins:** Ninguno

### 6.3 Configuracion de TypeScript (`tsconfig.json`)

```json
{
  "extends": "astro/tsconfigs/strict",
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  }
}
```

**Caracteristicas:**
- **Extiende:** `astro/tsconfigs/strict` (verificacion estricta de TypeScript)
- **Alias de ruta:** `@/*` mapea a `src/*` (usado en importaciones como `@/components/...`, `@/layouts/...`, `@/data/...`)

---

## 7. Sistema de Animaciones

### 7.1 Animaciones de Entrada

**Keyframes:**
```css
@keyframes fade-in-up {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
```

**Clases de delay:**
| Clase | Delay |
|-------|-------|
| `.anim-delay-1` | 0.1s |
| `.anim-delay-2` | 0.2s |
| `.anim-delay-3` | 0.3s |
| `.anim-delay-4` | 0.4s |

**Uso:** Se aplican a secciones, tarjetas de experiencia, y elementos del Hero para crear un efecto de aparicion escalonada.

### 7.2 Scroll-Driven Animations

**Header:**
```css
header {
  animation: blur linear both;
  animation-timeline: scroll();
  animation-range: 0 500px;
}
```
El header comienza transparente y se vuelve una pastilla de vidrio esmerilado a medida que el usuario hace scroll.

**Secciones:**
```css
[data-section] {
  animation: fade-in-up linear both;
  animation-timeline: view();
  animation-range: entry 10% entry 100%;
}
```
Cada seccion con `data-section` se anima de entrada cuando entra en el viewport.

### 7.3 Soporte Reduced Motion

```css
@media (prefers-reduced-motion: reduce) {
  *,
  ::before,
  ::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
  }
}
```

Todas las animaciones se desactivan para usuarios que prefieren movimiento reducido.

---

## 8. Iconografia

### Componentes de Iconos (`src/components/icons/`)

| Icono | Archivo | Uso |
|-------|---------|-----|
| ArrowRight | `ArrowRight.astro` | Enlaces "Saber mas" en ExperienceItem |
| Briefcase | `Briefcase.astro` | Icono de seccion (no utilizado actualmente) |
| Code | `Code.astro` | Icono de codigo `< >` (no utilizado) |
| CodeDots | `CodeDots.astro` | Icono de seccion de proyectos |
| ExternalLink | `ExternalLink.astro` | Enlaces externos en tarjetas de proyectos |
| HeartFilled | `HeartFilled.astro` | Footer "hecho con amor" |
| Link | `Link.astro` | Icono de cadena (no utilizado) |
| LinkedIn | `LinkedIn.astro` | Enlace LinkedIn en Hero |
| Mail | `Mail.astro` | Enlace de email en Hero |
| Moon | `Moon.astro` | Tema oscuro en ThemeToggle |
| NextJS | `NextJS.astro` | Badge de tag "Next.js" en Projects |
| ProfileCheck | `ProfileCheck.astro` | Icono de perfil (no utilizado) |
| Sun | `Sun.astro` | Tema claro en ThemeToggle |
| System | `System.astro` | Tema del sistema en ThemeToggle |
| Tailwind | `Tailwind.astro` | Badge de tag "Tailwind CSS" en Projects |
| UserHeart | `UserHeart.astro` | Icono de usuario con corazon (no utilizado) |

**Total:** 16 iconos SVG, 10 en uso activo, 6 no utilizados.

---

## 9. Activos Estaticos (`public/`)

| Archivo | Tipo | Uso |
|---------|------|-----|
| `favicon.svg` | SVG | Icono del sitio en pestana del navegador |
| `me.png` | PNG | Foto personal en seccion AboutMe |
| `midudev.webp` | WebP | Avatar en seccion Hero |
| `porfolio.webp` | WebP | Imagen Open Graph para redes sociales |
| `noise.svg` | SVG | Textura de grano para efecto visual |
| `projects/mee2027.webp` | WebP | Captura del proyecto MEE2027 |
| `projects/astro.webp` | WebP | Captura del proyecto de analisis astronomico |
| `projects/adventjs.webp` | WebP | Captura de AdventJS (no utilizada actualmente) |
| `projects/svgl.webp` | WebP | Captura de SVGL (no utilizada actualmente) |

---

## 10. Scripts Disponibles

| Script | Comando | Descripcion |
|--------|---------|-------------|
| `dev` | `astro dev` | Servidor de desarrollo con hot reload |
| `start` | `astro dev` | Alias de `dev` |
| `build` | `astro check && astro build` | Verificacion de tipos + construccion estatica |
| `preview` | `astro preview` | Vista previa de la construccion |
| `astro` | `astro` | CLI de Astro |

---

## 11. Observaciones y Mejoras Pendientes

### Inconsistencias en Datos
1. **projects.json:** El titulo del segundo proyecto es "AdventJS" pero la descripcion corresponde a una aplicacion de analisis de imagenes astronomicas.
2. **Tags sin mapear:** Los tags REACT, ZUSTAND, AXIOS, FASTAPI, PYTHON, ASTROPY del `projects.json` no estan definidos en el objeto `TAGS` de `Projects.astro`, por lo que no se renderizan con iconos ni colores personalizados.

### Contenido sin Renombrar
1. **Footer:** Todavia referencia a "midudev" en el texto de copyright.
2. **JSON-LD:** El schema Person en `Layout.astro` describe a "Miguel Angel Duran" en lugar de "Captwhite".
3. **Imagen Hero:** Usa `midudev.webp` como avatar.

### Bilinguismo
- Navegacion en catalan: "Experiencia", "Projectes", "Sobre mi", "Contacte"
- Algunos textos en espanol: "Hecho con", "Saber mas", "Preview"
- Contenido biografico en catalan

### Iconos No Utilizados
- `Code.astro`, `Link.astro`, `ProfileCheck.astro`, `Briefcase.astro`, `CodeDots.astro`, `UserHeart.astro` estan definidos pero no importados por ningun componente.

### Imagenes No Utilizadas
- `projects/adventjs.webp` y `projects/svgl.webp` existen en `/public/projects/` pero no se referencian en `projects.json`.
