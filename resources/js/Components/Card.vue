<template>
  <div class="min-h-screen flex flex-col sm:justify-center items-center pt-6 sm:pt-0 bg-gray-100">
    <a href="https://discord.gg/AxpvUWMR">
      <div v-if="name === 'Discord'"
           class="w-full sm:max-w-md mt-6 px-6 py-4 bg-indigo-400 shadow-md overflow-hidden flex flex-col  sm:justify-center items-center sm:rounded-lg"
           style="height:25rem; width: 20rem">
        <DiscordLogo></DiscordLogo>
        <h3 class="text-white text-2xl mt-4 font-bold">{{ name }}</h3>
      </div>
    </a>
  </div>
</template>
<script>
import DiscordLogo from "@/Components/Logos/DiscordLogo";

export default {
  components: {DiscordLogo},
  props: {
    name: '',
    color: '',
  }
}
</script>