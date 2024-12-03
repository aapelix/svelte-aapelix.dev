<script lang="ts">
  import { onMount } from 'svelte';
  import { fly, scale } from 'svelte/transition';
  import { Github, Twitter, Youtube } from 'lucide-svelte';
  import { scrollTo, scrollRef, scrollTop } from 'svelte-scrolling'

  let repos: Array<{
    name: string;
    url: string;
    description: string | null;
    language: string | null;
    stars: number;
    fork: boolean;
  }> = [];
  let limit = 5;

  export let animationClass = 'fade-in';

  const techIcons = [
    '/rust.png', '/csharp.png', '/astro.png', '/go.png', 
    '/java.png', '/svelte.png', '/cpp.png', '/python.png', 
    '/js.png', '/vue.png', '/c.png', '/react.png', '/ts.png'
  ];

  const featuredProjects = [
    {
      title: 'Jamix Menu v3',
      description: 'A remake of the jamix.com food menu app with NextJS with dark theme',
      technologies: ['NextJs', 'Typescript', 'motion.dev', 'TailwindCSS'],
      icon: '/react.png',
      link: 'https://jamix.aapelix.dev'
    },
    {
      title: 'abrw6',
      description: 'A hobby web browser made with Rust + gtk6 & webview6',
      technologies: ['Rust', 'gtk6', 'GtkWebView', 'adblock-rust'],
      icon: '/rust.png',
      link: 'https://github.com/aapelix/abrw6'
    },
    {
      title: 'weather.aapelix.dev',
      description: 'A simple weather app inspired by Nothing',
      technologies: ['Deno', 'Fresh', 'TailwindCSS', 'Preact'],
      icon: '/ts.png',
      link: 'https://weather.aapelix.dev'
    },
    {
      title: 'BlocksMined',
      description: 'Fabric mod for Minecraft 1.21 tracking player block mining',
      technologies: ['Java', 'Fabric', 'Minecraft'],
      icon: '/java.png',
      link: 'https://modrinth.com/mod/blocksmined'
    }
  ];

  async function getRepos() {
    try {
      const res = await fetch('https://api.github.com/users/aapelix/repos');
      const data = await res.json();

      repos = data.map((repo: { name: any; html_url: any; description: any; language: any; stargazers_count: any; fork: any; }) => ({
        name: repo.name,
        url: repo.html_url,
        description: repo.description,
        language: repo.language,
        stars: repo.stargazers_count,
        fork: repo.fork
      }));
    } catch (error) {
      console.error('Failed to fetch repos', error);
    }
  }

  let y: number;
  $: console.log(y);

  let mounted = false;

  onMount(() => {
    getRepos();
    mounted = true;
  });
</script>

<svelte:window bind:scrollY={y} />

<main class="bg-[#000000] text-white min-h-screen overflow-x-hidden font-mono">
  <nav class="absolute top-0 left-0 w-full z-50 flex gap-4 justify-center items-center mt-4 nav">
    <a class="hover:font-bold" use:scrollTo={"projects"}>About</a>
    <a class="hover:font-bold" use:scrollTo={"projects"}>Projects</a>
    <a class="hover:font-bold" use:scrollTo={"projects"}>Github</a>
  </nav>
  <header 
  class="relative h-screen flex items-center justify-center overflow-hidden bg-black text-white z-0"
>
  
  <div class="max-w-4xl px-6 text-center relative z-10">
    {#if mounted}
      <h1 
        class="text-6xl md:text-[8rem] font-bold tracking-tight text-transparent bg-clip-text"
        style="background-image: linear-gradient(90deg, #A5B4FC, #B0B4FF);"
        in:fly={{ y: 50, duration: 800 }}
      >
        aapelix
      </h1>
      
      <p 
        class="mt-4 text-xl md:text-2xl text-gray-400 font-light opacity-80"
        in:fly={{ y: 50, duration: 1000, delay: 200 }}
      >
        Software Developer
      </p>
      
      <div 
        class="mt-8 flex justify-center space-x-4"
        in:fly={{ y: 50, duration: 1000, delay: 400 }}
      >
        <!-- svelte-ignore a11y_missing_attribute -->
        <a 
          use:scrollTo={"projects"}
          class="px-6 py-3 border border-white/20 rounded-full backdrop-blur-md hover:bg-white/10 transition-all duration-300"
        >
          My projects
        </a>
        <a 
          href="https://aapelix.dev/git" 
          class="px-6 py-3 bg-[#A5B4FC] font-bold border border-white/10 rounded-full backdrop-blur-md hover:bg-white/20 transition-all duration-300"
        >
          GitHub
        </a>
      </div>
    {/if}
  </div>

  <div 
    class="absolute inset-0 pointer-events-none" 
    style="background: radial-gradient(circle at center, rgba(35,35,35,0.2) 0%, rgba(0,0,0,0.8) 100%);"
  ></div>
</header>

  <section class="py-16">
    <div class="overflow-hidden relative">
      <div class="flex animate-scroll">
        {#each [...techIcons, ...techIcons] as icon}
          <img 
            src={icon} 
            alt="Tech Icon"
            class="w-24 h-24 mx-4 transition-all duration-300 object-contain cursor-pointer tech-icon"
          />
        {/each}
      </div>
    </div>
  </section>
  
  <section use:scrollRef={"projects"} class="max-w-6xl mx-auto px-4 py-16" id="projects">
    {#if y > 350}
    <h2 
      class="text-5xl font-bold text-center mb-12 bg-clip-text text-transparent"
      style="background-image: linear-gradient(to right, rgba(165,180,252,1), rgba(129,140,248,1)); -webkit-background-clip: text;"
      in:fly={{y: 50, duration: 400}}
    >
      Featured Projects
    </h2>
    {/if}
    <div class="grid md:grid-cols-2 gap-8">
      {#each featuredProjects as project, index}
        {#if y > 450}
        <a 
          href={project.link} 
          target="_blank" 
          class="bg-[#111111] rounded-2xl p-6 transition-all duration-300 hover:scale-105 hover:bg-[#1a1a1a] border border-[#222222] hover:border-[#A5B4FC]/20"
          in:fly={{y: 50, duration: 400}}
          >
          <div class="flex items-center mb-4 gap-4">
            <img 
              src={project.icon} 
              alt={project.title} 
              class="w-12 h-12 rounded-xl object-contain"
            />
            <h3 class="text-2xl font-semibold">{project.title}</h3>
          </div>
          <p class="text-gray-400 mb-4">{project.description}</p>
          <div class="flex flex-wrap gap-2">
            {#each project.technologies as tech}
              <span 
                class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]"
              >
                {tech}
              </span>
            {/each}
          </div>
        </a>
        {/if}
      {/each}
    </div>
  </section>

  <section class="max-w-6xl mx-auto px-4 py-16">
    <h2 
      class="text-5xl font-bold text-center mb-12 bg-clip-text text-transparent"
      style="background-image: linear-gradient(to right, rgba(165,180,252,1), rgba(129,140,248,1)); -webkit-background-clip: text;"
    >
      All Repos
    </h2>

  <div class="space-y-6">
    {#each repos.slice(0, limit) as repo, index}
      {#if y > 800 + (index * 100)}
        <a 
          href={repo.url} 
          target="_blank" 
          class="{animationClass} block bg-[#111111] rounded-2xl p-6 transition-all duration-300 hover:scale-105 hover:bg-[#1a1a1a] border border-[#222222] hover:border-[#A5B4FC]/20"
          in:fly={{y: 50, duration: 400, delay: 500}}
        >
          <div class="flex items-center gap-4 mb-4">
            <Github class="text-[#A5B4FC] w-8 h-8" />
            <h3 class="text-2xl font-semibold">{repo.name}</h3>
          </div>
          {#if repo.description}
            <p class="text-gray-400 mb-4">{repo.description}</p>
          {/if}
          <div class="flex gap-4">
            {#if repo.language}
              <span 
                class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]"
              >
                {repo.language}
              </span>
            {/if}
            <span 
              class="text-xs px-3 py-1 rounded-full bg-[#181818] text-gray-400"
            >
              {repo.stars} stars
            </span>
            {#if repo.fork}
              <span 
                class="text-xs px-3 py-1 rounded-full bg-[#181818] text-gray-400"
              >
                Forked
              </span>
            {/if}
          </div>
        </a>
      {/if}
    {/each}
    
    <div class="flex justify-center gap-4 mt-8">
      <button 
        on:click={() => limit = Math.min(limit + 5, repos.length)}
        class="px-6 py-3 rounded-xl bg-[#111111] border border-[#222222] hover:border-[#A5B4FC]/20 transition-all duration-300"
      >
        Show more
      </button>
      <button 
        on:click={() => limit = Math.max(5, limit - 5)}
        disabled={limit <= 5}
        class="px-6 py-3 rounded-xl bg-[#111111] border border-[#222222] hover:border-[#A5B4FC]/20 transition-all duration-300 disabled:opacity-50"
      >
        Show less
      </button>
    </div>
  </div>
  </section>

  <section class="max-w-6xl mx-auto px-4 py-16">
    <h2 
      class="text-5xl font-bold text-center mb-12 bg-clip-text text-transparent"
      style="background-image: linear-gradient(to right, rgba(165,180,252,1), rgba(129,140,248,1)); -webkit-background-clip: text;"
    >
      My Socials
    </h2>
    
    <div class="flex justify-center md:gap-8 gap-2 flex-wrap">
      <a 
        href="https://github.com/aapelix" 
        target="_blank" 
        class="group flex items-center gap-4 bg-[#111111] rounded-2xl px-6 py-4 transition-all duration-300 hover:scale-110 hover:bg-[#1a1a1a] border border-[#222222] hover:border-[#A5B4FC]/20"
      >
        <Github class="text-[#A5B4FC] w-8 h-8 group-hover:scale-110 transition-transform" />
        <span>GitHub</span>
      </a>
      <a 
        href="https://youtube.com/@aapelix" 
        target="_blank" 
        class="group flex items-center gap-4 bg-[#111111] rounded-2xl px-6 py-4 transition-all duration-300 hover:scale-110 hover:bg-[#1a1a1a] border border-[#222222] hover:border-[#A5B4FC]/20"
      >
        <Youtube class="text-[#A5B4FC] w-8 h-8 group-hover:scale-110 transition-transform" />
        <span>YouTube</span>
      </a>
      <a 
        href="https://x.com/@aapelix1" 
        target="_blank" 
        class="group flex items-center gap-4 bg-[#111111] rounded-2xl px-6 py-4 transition-all duration-300 hover:scale-110 hover:bg-[#1a1a1a] border border-[#222222] hover:border-[#A5B4FC]/20"
      >
        <Twitter class="text-[#A5B4FC] w-8 h-8 group-hover:scale-110 transition-transform" />
        <span>Twitter</span>
      </a>
    </div>
  </section>

</main>

<style lang="postcss">
  @keyframes scroll {
    0% { transform: translateX(0); }
    100% { transform: translateX(-50%); }
  }

  .animate-scroll {
    display: flex;
    animation: scroll 20s linear infinite;
  }

  .tech-icon {
    filter: grayscale(1) sepia(1) hue-rotate(200deg) saturate(1.5) brightness(1.3);
    mix-blend-mode: lighten;
    transition: transform 0.3s ease, background 0.3s ease;
  }

  .tech-icon:hover {
    filter: none;
  }
</style>