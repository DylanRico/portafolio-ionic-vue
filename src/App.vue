<script setup>
import { onMounted, onUnmounted, ref } from 'vue'
import { IonApp, IonButton } from '@ionic/vue'
import NeuralMesh from './components/NeuralMesh.vue'
import { profile } from './profile'

const likes = ref(0), dislikes = ref(0), forceMotion = ref(true)
let observer
try { const saved = JSON.parse(localStorage.getItem('portfolio-reactions') || '{}'); likes.value = Number.isSafeInteger(saved.likes) && saved.likes >= 0 ? saved.likes : 0; dislikes.value = Number.isSafeInteger(saved.dislikes) && saved.dislikes >= 0 ? saved.dislikes : 0 } catch { /* El contador sigue funcionando durante la visita. */ }
function react(kind) {
  if (kind === 'like') likes.value++
  else dislikes.value++
  try { localStorage.setItem('portfolio-reactions', JSON.stringify({ likes: likes.value, dislikes: dislikes.value })) } catch { /* El almacenamiento puede estar desactivado. */ }
}
onMounted(() => {
  const id = location.hash.slice(1)
  if (['inicio', 'sobre-mi', 'formacion', 'experiencia', 'contacto'].includes(id)) document.getElementById(id)?.scrollIntoView()
  observer = new IntersectionObserver(entries => entries.forEach(entry => { if (entry.isIntersecting) { entry.target.classList.add('revealed'); observer.unobserve(entry.target) } }), { threshold: .08 })
  document.querySelectorAll('.reveal').forEach(el => observer.observe(el))
})
onUnmounted(() => observer?.disconnect())
</script>

<template>
  <IonApp :class="forceMotion ? 'motion-enabled' : 'motion-paused'">
    <header class="topbar"><a class="brand" href="#inicio"><span class="brand-mark">✳</span> {{ profile.name }} {{ profile.surname }} <span class="brand-sub">/ Portafolio</span></a><nav aria-label="Navegación principal"><a href="#sobre-mi">Perfil</a><a href="#formacion">Formación</a><a href="#experiencia">Experiencia</a></nav><a class="nav-contact" href="#contacto">Hablemos <span>↗</span></a></header>
    <main>
      <section id="inicio" class="hero"><NeuralMesh :force-motion="forceMotion" /><div class="hero-inner"><div class="hero-badge"><span>✳</span> PORTAFOLIO PERSONAL <i>·</i> 2026</div><h1>Ideas que conectan.<br><span>Experiencias que crean.</span></h1><p>{{ profile.introduction }}</p><div class="hero-actions"><IonButton href="#sobre-mi" class="primary-btn">Explorar mi perfil <span aria-hidden="true">↗</span></IonButton><a class="text-link" href="#experiencia">Ver mi experiencia <span>↓</span></a></div><button class="motion-toggle" type="button" :aria-pressed="forceMotion" @click="forceMotion = !forceMotion">{{ forceMotion ? '◉ Pausar animaciones' : '▷ Activar animaciones' }}</button></div><a class="hero-scroll" href="#sobre-mi">DESLIZA PARA EXPLORAR <span>↓</span></a></section>

      <div class="skills-strip" aria-label="Áreas de interés"><span>✳ <b>Desarrollo web</b></span><span>◇ <b>Infraestructura</b></span><span>⌘ <b>Software</b></span><span>◎ <b>Ciberseguridad</b></span><span>▣ <b>Escritorio</b></span></div>

      <section id="sobre-mi" class="intro section"><div class="section-tag reveal"><span>01</span> SOBRE MÍ</div><div class="intro-grid"><h2 class="reveal">La tecnología comienza con una <span>buena idea.</span></h2><p class="reveal">Soy {{ profile.name }} {{ profile.surname }}, tecnólogo en desarrollo de software. Me interesa crear experiencias web y entender la infraestructura que las hace posibles.</p></div><div class="profile-panel reveal"><div class="profile-visual"><span class="visual-orbit orbit-one"></span><span class="visual-orbit orbit-two"></span><span class="visual-center">DT<span>✳</span></span><div class="visual-label">CONSTRUYENDO IDEAS<br><strong>01 / IDENTIDAD</strong></div></div><div class="profile-copy"><span class="small-label">EL PUNTO DE PARTIDA</span><h3>Mucho gusto,<br>soy {{ profile.name }}.</h3><p>{{ profile.role }} con interés en desarrollo web, infraestructura y seguridad digital.</p><div class="details"><div><span>NOMBRE</span><strong>{{ profile.name }} {{ profile.surname }}</strong></div><div><span>UBICACIÓN / DIRECCIÓN</span><strong>{{ profile.address }}</strong></div><div><span>GÉNERO</span><strong>{{ profile.gender }}</strong></div><div><span>CORREO DE EJEMPLO</span><a :href="`mailto:${profile.email}`">{{ profile.email }} ↗</a></div></div></div></div></section>

      <section id="formacion" class="section education"><div class="section-head reveal"><div><div class="section-tag"><span>02</span> FORMACIÓN</div><h2>Aprender para<br><span>ir más lejos.</span></h2></div><p>Cada conocimiento es una pieza más para construir soluciones con propósito.</p></div><div class="card-grid"><article class="feature-card reveal"><div class="card-art art-code"><span class="art-icon">{ }</span><span class="art-caption">DESARROLLO / SOFTWARE</span></div><div class="card-body"><span class="small-label">{{ profile.education[0].date }}</span><h3>{{ profile.education[0].title }}</h3><p>{{ profile.education[0].detail }}</p><span class="card-link">Formación tecnológica <span>↗</span></span></div></article><article class="feature-card reveal"><div class="card-art art-security"><span class="shield">✳</span><span class="art-caption">SEGURIDAD / FUNDAMENTOS</span></div><div class="card-body"><span class="small-label">{{ profile.education[1].date }}</span><h3>{{ profile.education[1].title }}</h3><p>{{ profile.education[1].detail }}</p><span class="card-link">Formación complementaria <span>↗</span></span></div></article></div></section>

      <section id="experiencia" class="section experience"><div class="section-head reveal"><div><div class="section-tag"><span>03</span> EXPERIENCIA</div><h2>Del aprendizaje<br><span>a la acción.</span></h2></div><p>Una mirada a las áreas en las que he comenzado a construir mi camino profesional.</p></div><div class="experience-row reveal"><div class="experience-copy"><span class="small-label">{{ profile.experience[0].date }} DE EXPERIENCIA</span><h3>Programación e<br>infraestructura.</h3><p>{{ profile.experience[0].detail }}</p><a class="pill-link" href="#contacto">Conversemos <span>↗</span></a></div><div class="experience-art"><div class="terminal"><div class="terminal-top"><span>● ● ●</span><span>perfil / experiencia</span><span>↗</span></div><div class="terminal-body"><span>01 <i>ÁREA</i></span><strong>Infraestructura<span> + </span>código</strong><div class="terminal-line"></div><span>02 <i>ENFOQUE</i></span><strong>Construir y aprender<span>_</span></strong><div class="terminal-tags"><span>DESARROLLO</span><span>INFRAESTRUCTURA</span></div></div></div></div></div><div class="mini-grid"><article class="mini-card reveal"><span class="mini-icon">↗</span><span class="small-label">EXPERIENCIA PRÁCTICA</span><h3>Desarrollo web</h3><p>Creación de páginas y experiencias digitales para la web.</p></article><article class="mini-card reveal"><span class="mini-icon">▣</span><span class="small-label">EXPLORACIÓN</span><h3>Aplicaciones de escritorio</h3><p>Primer acercamiento al desarrollo de aplicaciones ejecutables.</p></article></div></section>

      <section id="contacto" class="section contact"><div class="contact-card reveal"><div class="contact-glow"></div><div class="contact-content"><span class="small-label">04 / SIGAMOS EN CONTACTO</span><h2>Las buenas ideas<br><span>empiezan aquí.</span></h2><p>Gracias por pasar por este espacio. ¿Conectamos?</p><a class="pill-link light-pill" :href="`mailto:${profile.email}`">Escríbeme <span>↗</span></a></div><div class="reaction"><span>¿QUÉ TE PARECIÓ?</span><div><button type="button" @click="react('like')" :aria-label="`Me gusta: ${likes}`">👍 <b>{{ likes }}</b></button><button type="button" @click="react('dislike')" :aria-label="`No me gusta: ${dislikes}`">👎 <b>{{ dislikes }}</b></button></div><small>Contador guardado en este navegador.</small></div></div></section>
    </main><footer><a class="brand" href="#inicio"><span class="brand-mark">✳</span> {{ profile.name }} {{ profile.surname }} <span class="brand-sub">/ Portafolio</span></a><span>PERFIL DE EJEMPLO · 2026</span><a v-if="profile.website" :href="profile.website" target="_blank" rel="noopener noreferrer">Sitio web de ejemplo ↗</a></footer>
  </IonApp>
</template>
