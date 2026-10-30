# Portafolio con Ionic y Vue

Página de presentación académica hecha con Vue 3, Ionic y Vite. Los datos iniciales son ejemplos: edita `src/profile.js` antes de compartirla. Evita publicar una dirección personal si no deseas que sea pública. El enlace del pie aparece al completar `website` con una URL completa, por ejemplo `https://ejemplo.com`.

## Ejecutar

Instala Node.js con npm y abre una terminal en esta carpeta:

```sh
npm install
npm run dev
```

Abre la dirección local que muestra Vite. Para comprobar la versión de producción: `npm run build`.

## Subir a Vercel

Sube el proyecto a un repositorio Git y en Vercel selecciona **Add New → Project** e importa ese repositorio. Vercel puede detectar Vite; usa `npm run build` como comando de construcción y `dist` como directorio de salida. El despliegue requiere una cuenta y no se ha realizado desde este proyecto.

Los botones de reacción usan `localStorage`: el contador es individual por navegador y no es compartido entre visitantes. Las animaciones arrancan activadas por petición del propietario y pueden pausarse desde el inicio. La malla deja de dibujarse cuando la portada está fuera de pantalla o la pestaña está oculta. Capacitor no es necesario para publicar la web; se agregaría si se desea crear una aplicación móvil nativa.
