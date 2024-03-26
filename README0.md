dame un ejemplo de un proyecto angular en donde se crean dos componentes webs, uno es un contador y el otro un banner de imagenes

Claro, te puedo dar un ejemplo básico de cómo estructurar un proyecto Angular con dos componentes: un contador y un banner de imágenes. Este ejemplo asume que ya tienes un proyecto Angular creado y listo para usar. Si no es así, primero necesitas instalar Angular CLI y crear un nuevo proyecto con `ng new nombre-del-proyecto`.

### Paso 1: Crear los componentes

Primero, vamos a crear dos componentes, `contador` y `banner`.

- Para el componente `contador`, usa el siguiente comando en tu terminal:
  ```
  ng generate component contador
  ```
- Para el componente `banner`, usa el siguiente comando:
  ```
  ng generate component banner
  ```

### Paso 2: Implementar el componente Contador

Abre el archivo `contador.component.ts` y reemplaza su contenido por lo siguiente:

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-contador',
  template: `
    <div>
      <h2>Contador: {{ contador }}</h2>
      <button (click)="incrementar()">Incrementar</button>
      <button (click)="decrementar()">Decrementar</button>
    </div>
  `,
  styleUrls: ['./contador.component.css']
})
export class ContadorComponent {
  contador: number = 0;

  incrementar() {
    this.contador++;
  }

  decrementar() {
    this.contador--;
  }
}
```

### Paso 3: Implementar el componente Banner

Para el componente `banner`, necesitas algunas imágenes en tu proyecto. Puedes colocarlas en la carpeta `assets`. Este ejemplo usa imágenes `imagen1.jpg`, `imagen2.jpg`, etc., pero puedes usar las que prefieras.

Abre el archivo `banner.component.ts` y ajusta su contenido de la siguiente manera:

```typescript
import { Component } from '@angular/core';

@Component({
  selector: 'app-banner',
  template: `
    <div>
      <img [src]="imagenes[imagenActual]" style="width: 100%;">
      <button (click)="anterior()">Anterior</button>
      <button (click)="siguiente()">Siguiente</button>
    </div>
  `,
  styleUrls: ['./banner.component.css']
})
export class BannerComponent {
  imagenes: string[] = [
    'assets/imagen1.jpg',
    'assets/imagen2.jpg',
    // Añade más imágenes según sea necesario
  ];
  imagenActual: number = 0;

  siguiente() {
    this.imagenActual = (this.imagenActual + 1) % this.imagenes.length;
  }

  anterior() {
    this.imagenActual = (this.imagenActual - 1 + this.imagenes.length) % this.imagenes.length;
  }
}
```

### Paso 4: Usar los componentes en tu aplicación

Finalmente, para ver tus componentes en acción, puedes incluirlos en el archivo `app.component.html` de tu aplicación. Reemplaza el contenido de este archivo con:

```html
<div style="text-align:center">
  <app-contador></app-contador>
  <app-banner></app-banner>
</div>
```

Este código colocará ambos componentes, el contador y el banner de imágenes, uno debajo del otro en la página principal de tu aplicación.

### Conclusión

Ahora tienes un proyecto Angular con un componente de contador que incrementa y decrementa un número y un componente de banner que muestra imágenes y permite navegar entre ellas. Recuerda que para que esto funcione, debes tener imágenes en tu carpeta `assets` y referenciarlas correctamente en tu componente `banner`.