<script setup>
import { onMounted, onUnmounted, ref, watch } from 'vue'

const props = defineProps({ forceMotion: Boolean })
const canvas = ref(null)
let cleanup = () => {}
let refresh = () => {}
onMounted(() => {
  const el = canvas.value, hero = el.parentElement, ctx = el.getContext('2d')
  if (!ctx) return
  const pointer = { x: 0, y: 0, targetX: 0, targetY: 0, strength: 0, active: false }
  let points = [], width = 0, height = 0, frame = 0, visible = true, start = 0, previous = 0
  function draw(now = 0) {
    frame = 0
    if (!visible || document.hidden) return
    if (!start) start = now
    const easing = 1 - Math.exp(-Math.min((now - (previous || now)) / 1000, .05) * 8)
    previous = now
    pointer.x += (pointer.targetX - pointer.x) * easing
    pointer.y += (pointer.targetY - pointer.y) * easing
    pointer.strength += ((pointer.active ? 1 : 0) - pointer.strength) * easing
    const reduced = !props.forceMotion
    const time = reduced ? 0 : (now - start) / 1000
    ctx.clearRect(0, 0, width, height)
    for (const p of points) {
      const angle = p.angle + time * p.speed
      let x = p.cx + Math.cos(angle) * p.radiusX, y = p.cy + Math.sin(angle) * p.radiusY
      if (pointer.strength > .001 && !reduced) {
        const dx = x - pointer.x, dy = y - pointer.y, distance = Math.hypot(dx, dy)
        if (distance < 210 && distance > 0) {
          const force = (1 - distance / 210) ** 2 * 55 * pointer.strength
          x += (dx - dy * .75) / distance * force; y += (dy + dx * .75) / distance * force
        }
      }
      p.x = x; p.y = y
    }
    for (let i = 0; i < points.length; i++) {
      const p = points[i]
      for (let j = i + 1; j < points.length; j++) {
        const q = points[j], dx = p.x - q.x, dy = p.y - q.y, distanceSquared = dx * dx + dy * dy
        if (distanceSquared < 15625) {
          const distance = Math.sqrt(distanceSquared)
          ctx.strokeStyle = `rgba(166, 219, 188, ${(1 - distance / 125) * .28})`
          ctx.beginPath(); ctx.moveTo(p.x, p.y); ctx.lineTo(q.x, q.y); ctx.stroke()
        }
      }
      ctx.fillStyle = `rgba(211, 244, 219, ${p.opacity})`
      ctx.beginPath(); ctx.arc(p.x, p.y, p.size, 0, Math.PI * 2); ctx.fill()
    }
    if (!reduced) frame = requestAnimationFrame(draw)
  }
  function resize() {
    const rect = el.getBoundingClientRect(), ratio = Math.min(devicePixelRatio || 1, 2)
    width = rect.width; height = rect.height; el.width = width * ratio; el.height = height * ratio
    ctx.setTransform(ratio, 0, 0, ratio, 0, 0)
    const centerX = width * .5, centerY = height * .5
    const radiusX = Math.min(width * .48, 700), radiusY = Math.min(height * .47, 440)
    // ponytail: capped quadratic neighbor scan; use spatial partitioning if point density grows.
    const count = Math.min(125, Math.max(68, Math.round(width * height / 7000)))
    points = Array.from({ length: count }, (_, i) => {
      const radius = Math.sqrt((i + .5) / count), angle = i * 2.399963
      return { cx: centerX, cy: centerY, angle, radiusX: radius * radiusX, radiusY: radius * radiusY, speed: .12 + (1 - radius) * .12, size: 1 + Math.random() * 1.3, opacity: .5 + Math.random() * .5 }
    })
    restart()
  }
  function restart() { cancelAnimationFrame(frame); frame = 0; start = 0; previous = 0; draw(performance.now()) }
  refresh = restart
  function move(event) {
    if (event.pointerType === 'touch') return
    const rect = el.getBoundingClientRect()
    pointer.targetX = event.clientX - rect.left; pointer.targetY = event.clientY - rect.top
    if (!pointer.active && pointer.strength < .01) { pointer.x = pointer.targetX; pointer.y = pointer.targetY }
    pointer.active = true
  }
  function leave() { pointer.active = false }
  function visibility() { if (!document.hidden) restart(); else cancelAnimationFrame(frame) }
  const observer = new IntersectionObserver(([entry]) => { visible = entry.isIntersecting; if (visible) restart(); else cancelAnimationFrame(frame) })
  observer.observe(hero)
  hero.addEventListener('pointermove', move); hero.addEventListener('pointerleave', leave)
  window.addEventListener('resize', resize); document.addEventListener('visibilitychange', visibility)
  resize()
  cleanup = () => { cancelAnimationFrame(frame); observer.disconnect(); hero.removeEventListener('pointermove', move); hero.removeEventListener('pointerleave', leave); window.removeEventListener('resize', resize); document.removeEventListener('visibilitychange', visibility) }
})
onUnmounted(() => cleanup())
watch(() => props.forceMotion, () => refresh())
</script>
<template><canvas ref="canvas" class="mesh" aria-hidden="true"></canvas></template>
