<template>
  <div class="overflow-y-hidden">
    <canvas ref="canvas"></canvas>
    <div
      id="container"
      class="absolute flex flex-col items-center"
      style="top: 50%; left: 50%; transform: translate(-50%, -50%)"
    >
      <h2 id="name" class="text-white text-xl opacity-0">
        Ti K K Wi
      </h2>
      <h2
        id="quote"
        class="text-white text-center font-exo mt-4 text-4xl opacity-0"
      >
        ONE WITH AN EVERLASTING DESIRE <br />FOR THE UNKNOWN & UNTOLD
      </h2>
      <a
        id="work"
        href="#"
        class="text-white mt-8 border-2 capitalize px-6 py-3 rounded-lg opacity-0"
      >
        view work</a
      >
    </div>
  </div>
</template>

<script lang="ts">
import {
  BufferAttribute,
  BufferGeometry,
  DirectionalLight,
  DoubleSide,
  Float32BufferAttribute,
  Light,
  Mesh,
  MeshPhongMaterial,
  PerspectiveCamera,
  PlaneGeometry,
  Points,
  PointsMaterial,
  Raycaster,
  Scene,
  WebGLRenderer
} from "three";
import gsap from "gsap";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";

export default {
  mounted() {
    const renderer: WebGLRenderer = new WebGLRenderer({
      //@ts-ignore
      canvas: this.$refs.canvas
    });

    renderer.setSize(innerWidth, innerHeight);
    renderer.setPixelRatio(devicePixelRatio);

    const scene: Scene = new Scene();

    const camera: PerspectiveCamera = new PerspectiveCamera(
      75,
      innerWidth / innerHeight,
      0.1,
      1000
    );

    const raycaster: Raycaster = new Raycaster();

    const planeMesh: Mesh = new Mesh(
      new PlaneGeometry(400, 400, 50, 50),
      new MeshPhongMaterial({
        side: DoubleSide,
        vertexColors: true,
        flatShading: true
      })
    );

    const { array } = planeMesh.geometry.attributes.position;
    const randomPosition = [];
    const color = [];

    //alter linear position..
    for (let i = 0; i < array.length; i++) {
      if (i % 3 === 0) {
        //@ts-ignore
        array[i] += (Math.random() - 0.5) * 3;
        //@ts-ignore
        array[i + 1] += (Math.random() - 0.5) * 3;
        //@ts-ignore
        array[i + 2] += (Math.random() - 0.5) * 3;
      }
      randomPosition.push(Math.random() * Math.PI * 2);
    }

    for (let i = 0; i < array.length / 3; i++) {
      color.push(0, 0.19, 0.4);
    }

    planeMesh.geometry.setAttribute(
      "color",
      new BufferAttribute(new Float32Array(color), 3)
    );

    //@ts-ignore
    planeMesh.geometry.attributes.position.randomPosition = randomPosition;
    //@ts-ignore
    planeMesh.geometry.attributes.position.originalPosition = array;

    const backLight: Light = new DirectionalLight(0xffffff, 1);
    backLight.position.set(0, -1, 1);
    const frontLight: Light = new DirectionalLight(0xffffff, 1);
    frontLight.position.set(0, 0, -1);

    const starGeometry: BufferGeometry = new BufferGeometry();
    const starMaterial: PointsMaterial = new PointsMaterial({
      color: 0xffffff
    });
    const starPositions = [];
    for (let i = 0; i < 10000; i++) {
      const x = (Math.random() - 0.5) * 2000;
      const y = (Math.random() - 0.5) * 2000;
      const z = (Math.random() - 0.5) * 2000;

      starPositions.push(x, y, z);
    }
    starGeometry.setAttribute(
      "position",
      new Float32BufferAttribute(starPositions, 3)
    );
    const stars = new Points(starGeometry, starMaterial);

    camera.position.z = 50;
    scene.add(planeMesh);
    scene.add(backLight);
    scene.add(frontLight);
    scene.add(stars);

    new OrbitControls(camera, renderer.domElement);

    let frame = 0;
    let mouse = {
      x: undefined,
      y: undefined
    };

    const originalColor = {
      r: 0,
      g: 0.19,
      b: 0.4
    };

    console.log(planeMesh.geometry.attributes.position);
    function animate() {
      requestAnimationFrame(animate);
      renderer.render(scene, camera);
      frame += 0.01;

      // move the verticies
      //@ts-ignore
      const { array, randomPosition } = planeMesh.geometry.attributes.position;
      for (let i = 0; i < array.length; i += 3) {
        //@ts-ignore
        array[i] += Math.cos(frame + randomPosition[i]) * 0.01;
        //@ts-ignore
        array[i + 1] += Math.sin(frame + randomPosition[i + 1]) * 0.01;
      }
      planeMesh.geometry.attributes.position.needsUpdate = true;

      const hoverColor = {
        r: 0.1,
        g: 0.5,
        b: 1
      };

      //raycast
      //@ts-ignore
      raycaster.setFromCamera(mouse, camera);
      const intersects = raycaster.intersectObject(planeMesh);

      if (intersects.length > 0) {
        //@ts-ignore
        const { color } = intersects[0].object.geometry.attributes;
        color.setX(intersects[0].face?.a, hoverColor.r);
        color.setY(intersects[0].face?.a, hoverColor.g);
        color.setZ(intersects[0].face?.a, hoverColor.b);

        color.setX(intersects[0].face?.b, hoverColor.r);
        color.setY(intersects[0].face?.b, hoverColor.g);
        color.setZ(intersects[0].face?.b, hoverColor.b);

        color.setX(intersects[0].face?.c, hoverColor.r);
        color.setY(intersects[0].face?.c, hoverColor.g);
        color.setZ(intersects[0].face?.c, hoverColor.b);
        //@ts-ignore
        color.needsUpdate = true;

        gsap.to(hoverColor, {
          r: originalColor.r,
          g: originalColor.g,
          b: originalColor.b,
          duration: 1,
          onUpdate: () => {
            //@ts-ignore
            color.setX(intersects[0].face.a, hoverColor.r);
            //@ts-ignore
            color.setY(intersects[0].face.a, hoverColor.g);
            //@ts-ignore
            color.setZ(intersects[0].face.a, hoverColor.b);

            //@ts-ignore
            color.setX(intersects[0].face.b, hoverColor.r);
            //@ts-ignore
            color.setY(intersects[0].face.b, hoverColor.g);
            //@ts-ignore
            color.setZ(intersects[0].face.b, hoverColor.b);

            //@ts-ignore
            color.setX(intersects[0].face.c, hoverColor.r);
            //@ts-ignore
            color.setY(intersects[0].face.c, hoverColor.g);
            //@ts-ignore
            color.setZ(intersects[0].face.c, hoverColor.b);
            color.needsUpdate = true;
          }
        });
      }
      stars.rotation.x += 0.0005;
    }

    animate();

    addEventListener("mousemove", ({ clientX, clientY }) => {
      // normalize to the range from -1 , 0 to 1
      // @ts-ignore
      mouse.x = (clientX / innerWidth) * 2 - 1;
      // @ts-ignore
      mouse.y = -(clientY / innerHeight) * 2 + 1;
    });

    gsap.to(document.getElementById("name"), {
      autoAlpha: 1,
      y: -5,
      duration: 1.3,
      ease: "power2"
    });

    gsap.to(document.getElementById("quote"), {
      autoAlpha: 1,
      y: -5,
      duration: 1.3,
      delay: 0.3,
      ease: "power3"
    });

    gsap.to(document.getElementById("work"), {
      autoAlpha: 1,
      y: -5,
      yoyo: true,
      duration: 1.5,
      delay: 0.6,
      ease: "slow"
    });

    document.getElementById("work")?.addEventListener("click", event => {
      event.preventDefault();
      gsap.to(camera.rotation, {
        x: 1.3,
        duration: 2
      });

      gsap.to(camera.position, {
        y: 500,
        duration: 3,
        delay: 2,
        onComplete: () => {
          this.$router.push("/work");
        }
      });
    });
  }
};
</script>
