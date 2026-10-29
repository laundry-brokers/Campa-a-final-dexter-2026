<script setup>
import { reactive, ref } from 'vue'
import SmartImg from './components/SmartImg.vue'
import Countdown from './components/Countdown.vue'

//  CONFIG 
const ENDPOINT = ''  

const year = new Date().getFullYear()
const menu = [
  { href: '#inicio', text: 'Inicio' },
  { href: 'https://dexterlaundrymexico.com/equipos/', text: 'Catálogo', external: true },
  { href: '#promocion', text: 'Promoción' }, { href: '#contacto', text: 'Contacto' }
]
const badges = [
  { i: '◇', t: 'Acero inoxidable' }, { i: '⏱', t: '10 años de garantía en lavadoras y 5 años en secadoras ' },
  { i: '◆', t: 'Fabricados para durar' }, { i: '▦', t: '132 años en la industria' }
]
const productos = [
  { title: 'Lavadoras Industriales', text: 'Máximo rendimiento, menor consumo de agua y energía.', cta: 'Ver lavadoras', url: 'https://dexterlaundrymexico.com/categoria-equipo/lavadoras-industriales/', img: 'imagen4' },
  { title: 'Secadoras Industriales', text: 'Secado rápido y eficiente para operaciones de alto rendimiento.', cta: 'Ver secadoras', url: 'https://dexterlaundrymexico.com/categoria-equipo/secadoras-industriales/', img: 'imagen2' },
  { title: 'Centros de Lavado', text: 'Lavadora y secadora en un solo equipo. Ideal para optimizar tu espacio.', cta: 'Ver centros de lavado', url: 'https://dexterlaundrymexico.com/categoria-equipo/lavadoras/centro-de-lavado-lavadoras/', img: 'imagen1' }
]
const perks = ['Asesoría personalizada', 'Planes a tu medida', 'Respuesta rápida', 'Expertos en lavandería industrial']
const tipos = ['Lavandería comercial', 'Lavandería autoservicio', 'Hotel / Hospitalidad', 'Hospital / Salud', 'Otro']

//  FORMULARIO 
const f = reactive({ nombre: '', empresa: '', ciudad: '', telefono: '', correo: '', tipo: '', mensaje: '', web: '' })
const err = reactive({})
const tocado = ref(false)
const enviando = ref(false)
const estado = reactive({ ok: false, msg: '' })
let ultimo = 0

const texto = /^[^<>]{2,}$/
const tel = /^[0-9+()\-\s]{8,20}$/
const mail = /^[^\s@<>]+@[^\s@<>]+\.[^\s@<>]{2,}$/

function validar () {
  Object.keys(err).forEach(k => delete err[k])
  if (!texto.test(f.nombre.trim())) err.nombre = 'Ingresa tu nombre.'
  if (!texto.test(f.empresa.trim())) err.empresa = 'Ingresa tu empresa.'
  if (!texto.test(f.ciudad.trim())) err.ciudad = 'Ingresa tu ciudad.'
  if (!tel.test(f.telefono.trim())) err.telefono = 'Teléfono no válido.'
  if (!mail.test(f.correo.trim())) err.correo = 'Correo no válido.'
  if (!tipos.includes(f.tipo)) err.tipo = 'Selecciona una opción.'
  if (/[<>]/.test(f.mensaje)) err.mensaje = 'Caracteres no permitidos.'
  return !Object.keys(err).length
}
const cls = k => (tocado.value ? { 'is-invalid': !!err[k], 'is-valid': !err[k] } : {})

async function enviar () {
  tocado.value = true; estado.msg = ''
  if (f.web) return                      // honeypot
  if (!validar()) return
  if (Date.now() - ultimo < 15000) { estado.ok = false; estado.msg = 'Espera unos segundos antes de reenviar.'; return }
  enviando.value = true
  try {
    if (ENDPOINT) {
      const { web, ...datos } = f
      const r = await fetch(ENDPOINT, { method: 'POST', headers: { 'Content-Type': 'application/json' }, credentials: 'same-origin', body: JSON.stringify(datos) })
      if (!r.ok) throw new Error('http')
    }
    ultimo = Date.now()
    estado.ok = true; estado.msg = '¡Gracias! Un experto te contactará pronto.'
    Object.keys(f).forEach(k => (f[k] = ''))
    tocado.value = false
  } catch {
    estado.ok = false; estado.msg = 'No se pudo enviar. Inténtalo de nuevo.'
  } finally { enviando.value = false }
}
</script>

<template>
  <!-- NAV -->
  <nav class="navbar navbar-expand-md navbar-dark nav-dx fixed-top">
    <div class="container">
      <a class="navbar-brand" href="#inicio"><SmartImg name="logo" alt="Dexter Laundry" eager class="logo" /></a>
      <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#menu" aria-controls="menu" aria-expanded="false" aria-label="Abrir menú">
        <span class="navbar-toggler-icon"></span>
      </button>
      <div id="menu" class="collapse navbar-collapse">
        <ul class="navbar-nav ms-auto align-items-md-center gap-md-4">
          <li v-for="l in menu" :key="l.href" class="nav-item"><a class="nav-link" :href="l.href" :target="l.external ? '_blank' : null" :rel="l.external ? 'noopener noreferrer' : null">{{ l.text }}</a></li>
          <li class="nav-item"><a class="btn btn-dx btn-nav" href="#contacto">Solicita tu cotización →</a></li>
        </ul>
      </div>
    </div>
  </nav>

  <!-- HERO -->
  <header id="inicio" class="hero">
    <div class="container">
      <div class="row">
        <div class="col-lg-6 hero-copy">
          <p class="promo-kicker"><span>Promoción Dexter</span></p>
          <h1 class="promo-h1"><span class="pct">10 %</span> de<br>descuento</h1>
          <p class="promo-line">En tu lavadora T-600 + secadora T-50</p>
          <p class="promo-strong">Hechas para durar</p>
          <p class="promo-sub">La garantía más extensa del mercado</p>
          <Countdown />
          <a class="btn btn-promo" href="#contacto">Solicita tu cotización </a>
          <ul class="hero-badges list-unstyled row row-cols-2 row-cols-sm-4 g-3 m-0">
            <li v-for="b in badges" :key="b.t" class="col"><span class="accent me-1">{{ b.i }}</span>{{ b.t }}</li>
          </ul>
        </div>
      </div>
    </div>
  </header>

  <!-- SOLUCIONES -->
  <section id="soluciones" class="sec-light">
    <div class="container">
      <p class="eyebrow">Nuestras soluciones</p>
      <div class="row align-items-end mb-4 g-3">
        <div class="col-lg-8">
          <h2 class="h-dx">Soluciones óptimas para<br class="d-none d-lg-block"> el sector de lavandería industrial</h2>
          <p class="text-secondary mb-0">Equipos diseñados para cada necesidad.</p>
        </div>
        <div class="col-lg-4"><p class="side-note mb-0">Rendimiento real<br>para grandes negocios.</p></div>
      </div>
      <div class="row g-4">
        <div v-for="p in productos" :key="p.title" class="col-lg-4">
          <article class="card-dx h-100">
            <div class="card-img"><SmartImg :name="p.img" :alt="p.title" /></div>
            <div class="card-txt">
              <div><h3>{{ p.title }}</h3><p>{{ p.text }}</p></div>
              <a class="btn btn-outline-dx btn-sm" :href="p.url" target="_blank" rel="noopener noreferrer">{{ p.cta }} →</a>
            </div>
          </article>
        </div>
      </div>
    </div>
  </section>

  <!-- PROMO -->
  <section id="promocion" class="sec-promo">
    <div class="container position-relative">
      <div class="row align-items-center g-5">
        <div class="col-lg-6">
          <p class="eyebrow">Promoción especial</p>
          <h2 class="display-dx promo-title">Aprovecha este Best Fin y lleva tu lavandería al <span class="accent">siguiente nivel con Dexter.</span></h2>
          <p class="promo-off"><strong>10% DE DESCUENTO</strong><br>EN MODELOS SELECCIONADOS.</p>
          <a class="btn btn-dx" href="#contacto">Solicita tu cotización →</a>
        </div>
        <div class="col-lg-6"><div class="promo-frame"><SmartImg name="imagen3" alt="Equipos Dexter en promoción" /></div></div>
      </div>
    </div>
  </section>

  <!-- CONTACTO -->
  <section id="contacto" class="sec-contact">
    <div class="container">
      <div class="row align-items-center g-5">
        <div class="col-lg-6">
          <p class="eyebrow">Contáctanos</p>
          <h2 class="h-dx text-white">Soluciones integrales en lavandería industrial.</h2>
          <p class="mb-4">Solicita tu cotización con un experto en Dexter.</p>
          <ul class="perks list-unstyled row row-cols-1 row-cols-sm-2 g-3">
            <li v-for="x in perks" :key="x" class="col d-flex align-items-center"><span class="perk-dot">✓</span>{{ x }}</li>
          </ul>
        </div>
        <div class="col-lg-6">
          <form class="form-dx" novalidate @submit.prevent="enviar">
            <div class="row g-3">
              <div class="col-sm-6">
                <label class="form-label" for="nombre">Nombre completo *</label>
                <input id="nombre" v-model="f.nombre" class="form-control" :class="cls('nombre')" maxlength="80" autocomplete="name" placeholder="Tu nombre">
                <div class="invalid-feedback">{{ err.nombre }}</div>
              </div>
              <div class="col-sm-6">
                <label class="form-label" for="empresa">Empresa *</label>
                <input id="empresa" v-model="f.empresa" class="form-control" :class="cls('empresa')" maxlength="100" autocomplete="organization" placeholder="Nombre de tu empresa">
                <div class="invalid-feedback">{{ err.empresa }}</div>
              </div>
              <div class="col-sm-6">
                <label class="form-label" for="ciudad">Ciudad *</label>
                <input id="ciudad" v-model="f.ciudad" class="form-control" :class="cls('ciudad')" maxlength="60" autocomplete="address-level2" placeholder="Ciudad">
                <div class="invalid-feedback">{{ err.ciudad }}</div>
              </div>
              <div class="col-sm-6">
                <label class="form-label" for="telefono">Teléfono *</label>
                <input id="telefono" v-model="f.telefono" type="tel" class="form-control" :class="cls('telefono')" maxlength="20" autocomplete="tel" placeholder="55 1234 5678">
                <div class="invalid-feedback">{{ err.telefono }}</div>
              </div>
              <div class="col-sm-6">
                <label class="form-label" for="correo">Correo electrónico *</label>
                <input id="correo" v-model="f.correo" type="email" class="form-control" :class="cls('correo')" maxlength="120" autocomplete="email" placeholder="tucorreo@empresa.com">
                <div class="invalid-feedback">{{ err.correo }}</div>
              </div>
              <div class="col-sm-6">
                <label class="form-label" for="tipo">Tipo de proyecto *</label>
                <select id="tipo" v-model="f.tipo" class="form-select" :class="cls('tipo')">
                  <option value="">Selecciona una opción</option>
                  <option v-for="t in tipos" :key="t" :value="t">{{ t }}</option>
                </select>
                <div class="invalid-feedback">{{ err.tipo }}</div>
              </div>
              <div class="col-12">
                <label class="form-label" for="mensaje">Mensaje</label>
                <textarea id="mensaje" v-model="f.mensaje" class="form-control" rows="3" maxlength="1000" placeholder="Cuéntanos sobre tu proyecto..."></textarea>
              </div>
              <div class="hp" aria-hidden="true"><label>No llenar <input v-model="f.web" type="text" tabindex="-1" autocomplete="off"></label></div>
              <div class="col-12">
                <button class="btn btn-dx w-100 py-2" type="submit" :disabled="enviando">{{ enviando ? 'Enviando…' : 'Enviar solicitud →' }}</button>
                <p v-if="estado.msg" class="small mt-2 mb-0 text-center" :class="estado.ok ? 'text-success' : 'text-danger'" role="status">{{ estado.msg }}</p>
                <p v-else class="small mt-2 mb-0 text-center text-secondary"> Tu información está segura con nosotros.</p>
              </div>
            </div>
          </form>
        </div>
      </div>
    </div>
  </section>

  <!-- FOOTER -->
  <footer class="footer-dx">
    <div class="container">
      <div class="row align-items-center gy-3">
        <div class="col-md-3"><SmartImg name="logo" alt="Dexter Laundry" class="logo-foot" /></div>
        <div class="col-md-3"><strong>México</strong><br><span class="text-secondary">Dexter Laundry México</span></div>
        <div class="col-md-3"><strong>Atención comercial</strong><br><span class="text-secondary">Solicita información y cotización</span></div>
        <div class="col-md-3 text-md-end"><a v-for="l in menu.filter(m => m.href !== '#promocion')" :key="l.href" :href="l.href" :target="l.external ? '_blank' : null" :rel="l.external ? 'noopener noreferrer' : null" class="me-3">{{ l.text }}</a></div>
      </div>
      <hr class="border-secondary opacity-25">
      <small class="text-secondary">© {{ year }} Dexter Laundry México. Mockup promocional Best Fin.</small>
    </div>
  </footer>
</template>
