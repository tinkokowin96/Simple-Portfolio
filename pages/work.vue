<template>
  <div class="bg-black h-screen p-5">
    <h1 class="font-exo py-4 text-white text-5xl capitalize">My Work</h1>
    <div class="flex space-x-4 pt-4">
      <div
        class="opacity-0"
        ref="projects"
        v-for="project in projects"
        v-bind:key="project.title"
      >
        <nuxt-link to="/">
          <img
            class="object-cover"
            style="height: 70vh;"
            :src="project.image.url"
            :alt="project.title"
          />
        </nuxt-link>
        <h3 class="text-white">
          {{ project.title }}
        </h3>
      </div>
    </div>
  </div>
</template>
<script>
import iphoneImg from "~/assets/iphone.jpg";
import laptopImg from "~/assets/laptop.jpg";
import personImg from "~/assets/person.jpg";
import gsap from "gsap";

export default {
  data() {
    return {
      projects: [
        {
          image: {
            url: iphoneImg
          },
          title: "IPHONE"
        },
        {
          image: {
            url: laptopImg
          },
          title: "LAPTOP"
        },
        {
          image: {
            url: personImg
          },
          title: "Person"
        },
        {
          image: {
            url: iphoneImg
          },
          title: "IPHONE"
        },
        {
          image: {
            url: laptopImg
          },
          title: "LAPTOP"
        },
        {
          image: {
            url: personImg
          },
          title: "Person"
        }
      ]
    };
  },
  mounted() {
    gsap.to(this.$refs.projects, {
      opacity: 1,
      duration: 1.2,
      stagger: 0.25,
      ease: "power3",
      delay: 0.5
    });
  }
};
</script>
