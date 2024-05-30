<template>
    <div ref="container" class="fixed w-full h-full top-0 left-0"></div>
</template>

<script>
import { ref, onMounted } from 'vue';
import { Scene, PerspectiveCamera, WebGLRenderer, Vector2, Mesh, SphereGeometry, MeshStandardMaterial, BoxGeometry, MeshNormalMaterial, BufferGeometry, Float32BufferAttribute, PointsMaterial, Points, DirectionalLight, AmbientLight, Raycaster } from 'three';
import { TrackballControls } from 'three/examples/jsm/controls/TrackballControls';

export default {
  setup() {
    const container = ref(null);
    
    const scene = new Scene();
    const camera = new PerspectiveCamera();
    const renderer = new WebGLRenderer();
    const mouse = new Vector2();
    let controls;
    let sphere;
    let box;
        
    const init = () => {
      if (container.value instanceof HTMLElement) {
        const { clientWidth, clientHeight } = container.value;
        
        container.value.addEventListener('mousemove', handleMouseMove);

        camera.aspect = clientWidth / clientHeight;
        camera.updateProjectionMatrix();
        camera.position.set(0, 0, +1000);
        camera.lookAt(0, 0, 0);

        controls = new TrackballControls(camera, container.value);

        controls.rotateSpeed = 5;
        controls.zoomSpeed = 2;
        controls.panSpeed = 0;

        renderer.setSize(clientWidth, clientHeight);
        renderer.setPixelRatio(clientWidth / clientHeight);
        container.value.appendChild(renderer.domElement);

        createBox();
        createSphere();
        createParticles();
        raycastCheck();

        // 平行光源
        const directionalLight = new DirectionalLight(0xFFFFFF);
        directionalLight.position.set(1, 1, 1);
        scene.add(directionalLight);

        const ambientlight = new AmbientLight(0xFFFFFF, 1.0);
        scene.add(ambientlight);
        
        animate();
      }
    };

    const animate = () => {
      const frame = () => {
        controls.update();
        renderer.render(scene, camera);
        requestAnimationFrame(frame);
      };
      frame();
    };
    
    const createSphere = () => {
      const geometry = new SphereGeometry(300, 30, 30);
      const material = new MeshStandardMaterial();
      sphere = new Mesh(geometry, material);
      scene.add(sphere);
    };

    const createBox = () => {
      const geometry = new BoxGeometry(100, 100, 100);
      const material = new MeshNormalMaterial();
      box = new Mesh(geometry, material);
      box.position.set(0, 340, 0);
      scene.add(box);
      tick();
      function tick() {
        requestAnimationFrame(tick);
      }
    }

    const createParticles = () => {
      const SIZE = 3000;
      const LENGTH = 1000;

      const vertices = [];
      for (let i = 0; i < LENGTH; i++) {
        const x = SIZE * (Math.random() - 0.5);
        const y = SIZE * (Math.random() - 0.5);
        const z = SIZE * (Math.random() - 0.5);

        vertices.push(x, y, z);
      }

      const geometry = new BufferGeometry();
      geometry.setAttribute('position', new Float32BufferAttribute(vertices, 3));

      const material = new PointsMaterial({
        size: 10,
        color: 0xffffff,
      });

      const mesh = new Points(geometry, material);
      scene.add(mesh);
    }

    const handleMouseMove = (event) => {
      const element = event.currentTarget;
      // canvas要素上のXY座標
      const x = event.clientX - element.offsetLeft;
      const y = event.clientY - element.offsetTop;
      // canvas要素の幅・高さ
      const w = element.offsetWidth;
      const h = element.offsetHeight;

      // -1〜+1の範囲で現在のマウス座標を登録する
      mouse.x = ( x / w ) * 2 - 1;
      mouse.y = -( y / h ) * 2 + 1;
    }

    const raycastCheck = () => {
      const raycaster = new Raycaster();

      tick();

      function tick() {
        raycaster.setFromCamera(mouse, camera);

        // その光線とぶつかったオブジェクトを得る
        const intersects = raycaster.intersectObjects(scene.children);

        if(intersects.length > 0 && box === intersects[0].object){
          box.scale.set( 1.5, 1.5, 1.5 );
        }
        else {
          box.scale.set( 1, 1, 1 );
        }

        requestAnimationFrame(tick);
      }
    }

    // マウント時に初期化して描画
    onMounted(() => {
      init();
    });

    return {
      container,
    };
  }
};
</script>

<style scoped>
div {
  width: 100%;
  height: 100vh;
  overflow: hidden;
}
</style>
  