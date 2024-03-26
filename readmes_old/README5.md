ahora haz que el servicio se encargue de todo lo relacionado con threejs

Para ajustar la implementación de tal manera que el servicio `ThreeSceneService` se encargue de toda la lógica relacionada con Three.js, moveremos la creación y manipulación de la escena, cámara, renderer, y cualquier objeto dentro de la escena Three.js al servicio. De esta manera, el componente se encargará únicamente de la presentación, delegando toda la gestión de Three.js al servicio.

### Paso 1: Actualizar el Servicio ThreeSceneService

Primero, vamos a expandir el servicio `ThreeSceneService` para que maneje la creación de la escena, el renderer, la cámara, y también para iniciar y detener la animación.

```typescript
import { Injectable, ElementRef } from '@angular/core';
import * as THREE from 'three';

@Injectable({
  providedIn: 'root'
})
export class ThreeSceneService {
  private scene = new THREE.Scene();
  private camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
  private renderer = new THREE.WebGLRenderer();
  private cube?: THREE.Mesh;
  private animationFrameId: number | null = null;

  constructor() {
    this.renderer.setSize(window.innerWidth, window.innerHeight);
    this.camera.position.z = 5;
    this.addCube();
  }

  addCube() {
    const geometry = new THREE.BoxGeometry();
    const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
    this.cube = new THREE.Mesh(geometry, material);
    this.scene.add(this.cube);
  }

  animate() {
    const render = () => {
      this.animationFrameId = requestAnimationFrame(render);
      if (this.cube) {
        this.cube.rotation.x += 0.01;
        this.cube.rotation.y += 0.01;
      }
      this.renderer.render(this.scene, this.camera);
    };
    render();
  }

  stop() {
    if (this.animationFrameId) {
      cancelAnimationFrame(this.animationFrameId);
      this.animationFrameId = null;
    }
  }

  getRendererDOMElement(): HTMLCanvasElement {
    return this.renderer.domElement;
  }

  initializeRenderer(elementRef: ElementRef) {
    elementRef.nativeElement.appendChild(this.getRendererDOMElement());
    this.animate();
  }

  ngOnDestroy() {
    this.stop();
  }
}
```

### Paso 2: Modificar el Componente ThreeScene

Ahora, actualizaremos el componente `ThreeSceneComponent` para que utilice el servicio `ThreeSceneService` para manejar todo relacionado con Three.js. El componente ahora será muy ligero, delegando responsabilidades al servicio.

```typescript
import { Component, ElementRef, OnDestroy, OnInit } from '@angular/core';
import { ThreeSceneService } from '../three-scene.service';

@Component({
  selector: 'app-three-scene',
  template: '',
  styleUrls: ['./three-scene.component.css']
})
export class ThreeSceneComponent implements OnInit, OnDestroy {

  constructor(private el: ElementRef, private threeSceneService: ThreeSceneService) {}

  ngOnInit(): void {
    this.threeSceneService.initializeRenderer(this.el);
  }

  ngOnDestroy(): void {
    this.threeSceneService.ngOnDestroy();
  }
}
```

### Paso 3: Uso y Pruebas

Con esta configuración, el servicio `ThreeSceneService` ahora se encarga de toda la lógica de Three.js, incluyendo la animación y la gestión del ciclo de vida del renderer y otros objetos de Three.js. El componente `ThreeSceneComponent` actúa como un contenedor que inicia el proceso al ser montado y se asegura de detener la animación cuando se desmonta, manteniendo una separación clara de responsabilidades.

Este enfoque tiene varias ventajas:

- **Centralización**: Toda la lógica de Three.js está en un solo lugar, lo que facilita la gestión y la actualización.
- **Reusabilidad**: Puedes utilizar el mismo servicio para alimentar diferentes componentes, compartiendo la escena o partes de ella entre varios puntos de tu aplicación.
- **Separación de responsabilidades**: El componente se enfoca en las interacciones del DOM y el ciclo de vida del componente Angular, mientras que el servicio maneja específicamente la lógica de Three.js.

Recuerda que, dependiendo de la complejidad de tu aplicación Three.js, es posible que necesites gestionar de forma más granular la creación y destrucción de objetos dentro de tu servicio para evitar fugas de memoria y garantizar el rendimiento.