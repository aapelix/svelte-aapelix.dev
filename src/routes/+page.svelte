<script>
    import { error } from "@sveltejs/kit";
    import { onMount } from "svelte";

    import { Github, Youtube, Twitter } from "lucide-svelte"
    import { tweened } from 'svelte/motion';
    import { cubicOut } from 'svelte/easing';

    /**
	 * @type {HTMLElement}
	 */
    let container;
    /**
	 * @type {HTMLDivElement}
	 */
    let image;
    let scrollPos = 0;
    const SCALE_FACTOR = 0.1;
    const SCROLL_THRESHOLD = 100;

    const smoothScale = tweened(1, {
      duration: 1500,
      easing: cubicOut
    });

    let ticking = false;

    function handleScroll() {
      if (!ticking) {
        requestAnimationFrame(() => {
          if (!container || !image) return;
          
          const scrollPos = window.scrollY;
          
          if (scrollPos <= SCROLL_THRESHOLD) {
            const scale = 1 - (scrollPos / SCROLL_THRESHOLD) * SCALE_FACTOR;
            smoothScale.set(scale);
            
            const borderRadius = Math.min(32, (scrollPos / SCROLL_THRESHOLD) * 32);
            image.style.borderRadius = `${borderRadius}px`;
          }
          
          ticking = false;
        });
        
        ticking = true;
      }
    }

    $: if (image) {
      image.style.transform = `scale(${$smoothScale})`;
    }

    onMount(() => {
      window.addEventListener('scroll', handleScroll, { passive: true });
      return () => {
        window.removeEventListener('scroll', handleScroll);
      };
    });

    /**
	 * @type {any[]}
	 */
    let repos = [];

    let limit = 5;
    
    export function load() {
      throw error(404, "Not found");
    }
    
    const quotes = [
      "flew to the moon", 
      "launched a nuclear missile to mars", 
      "ate uranium", 
      "bought a fighter jet", 
      "installed Arch Linux", 
      "planted an uranium tree on his garden",
    ];
    
    function randomQuote() {
      return quotes[Math.floor(Math.random() * quotes.length)];
    }
    let quote = randomQuote();
    
    /**
     * @type {HTMLDivElement}
     */
    let imageTrack;
    
    onMount(() => {
        getRepos();

      if (imageTrack) {
        const images = imageTrack.children;
        // @ts-ignore
        const totalWidth = Array.from(images).reduce((width, img) => width + 100 + 20, 0); // Fixed width + margin
        const viewportWidth = window.innerWidth;
        const setsNeeded = Math.ceil((viewportWidth * 2) / totalWidth);
        
        for (let i = 0; i < setsNeeded; i++) {
          Array.from(images).forEach(img => {
            const clone = img.cloneNode(true);
            imageTrack.appendChild(clone);
          });
        }
      }
    });

    async function getRepos() {
        const res = await fetch('https://api.github.com/users/aapelix/repos');
        const data = await res.json();

        repos = data.map((/** @type {{ name: any; html_url: any; description: any; language: any; stargazers_count: any; forks_count: any;  fork: any; }} */ repo) => ({
            name: repo.name,
            url: repo.html_url,
            description: repo.description,
            language: repo.language,
            stars: repo.stargazers_count,
            fork: repo.fork,
        }));
    }

</script>
    
<main class="min-h-screen bg-[#0A0A0A]">
  <header class="h-screen flex items-center justify-center relative overflow-hidden" bind:this={container}>
    <div
      bind:this={image}
      class="absolute inset-0 opacity-10"
      style="background-image: url('/g.png'); background-size: cover; background-position: center; filter: blur(8px);"
    >
    </div>
    <div class="relative z-10 text-center px-4">
      <h1 class="md:text-8xl text-5xl font-black bg-gradient-to-r from-[#A5B4FC] to-[#818CF8] bg-clip-text text-transparent">
        "It's me, aapelix"
      </h1>
      <p class="mt-4 text-xl md:text-2xl text-gray-300 font-light">he said and {quote}</p>
    </div>
  </header>

  <div class="max-w-6xl mx-auto px-4">
    <div class="slider-container w-full my-20">
      <div class="fade-overlay left"></div>
      <div class="infinite-scroll">
        <div class="image-track" bind:this={imageTrack}>
          <img src="/rust.png" alt="rust" class="tech-icon">
          <img src="/csharp.png" alt="csharp" class="tech-icon">
          <img src="/astro.png" alt="astro" class="tech-icon">
          <img src="/go.png" alt="go" class="tech-icon">
          <img src="/java.png" alt="java" class="tech-icon">
          <img src="/svelte.png" alt="svelte" class="tech-icon">
          <img src="/cpp.png" alt="cpp" class="tech-icon">
          <img src="/python.png" alt="python" class="tech-icon">
          <img src="/js.png" alt="js" class="tech-icon">
          <img src="/vue.png" alt="vue" class="tech-icon">
          <img src="/c.png" alt="c" class="tech-icon">
          <img src="/react.png" alt="react" class="tech-icon">
          <img src="/ts.png" alt="ts" class="tech-icon">
        </div>
      </div>
      <div class="fade-overlay right"></div>
    </div>
    <div class="featured-projects my-20">
      <h2 class="text-5xl font-bold text-center mb-10 bg-gradient-to-r from-[#A5B4FC] to-[#818CF8] bg-clip-text text-transparent">
        Featured Projects
      </h2>
      <div class="grid md:grid-cols-2 gap-6">
        <a target="blank" href="https://jamix.aapelix.dev" class="p-6 rounded-2xl bg-[#111111] border border-[#222222] hover:border-[#A5B4FC]/20 transition-all duration-300 hover:scale-[1.02]">
          <div class="flex items-center gap-3 mb-4">
            <div class="w-12 h-12 rounded-xl bg-[#A5B4FC]/10 flex items-center justify-center">
              <img src="/react.png" alt="rust" class="w-8 h-8 object-contain">
            </div>
            <h3 class="text-2xl font-semibold text-white">Jamix Menu v3</h3>
          </div>
          <p class="text-gray-400 mb-4">A remake of the jamix.com food menu app with NextJS with dark theme (the original version didn't have that)</p>
          <div class="flex gap-3">
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">NextJs</span>
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">Typescript</span>
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">motion.dev</span>
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">TailwindCSS</span>
          </div>
        </a>

        <a target="blank" href="https://github.com/aapelix/abrw6" class="p-6 rounded-2xl bg-[#111111] border border-[#222222] hover:border-[#A5B4FC]/20 transition-all duration-300 hover:scale-[1.02]">
          <div class="flex items-center gap-3 mb-4">
            <div class="w-12 h-12 rounded-xl bg-[#A5B4FC]/10 flex items-center justify-center">
              <img src="/rust.png" alt="typescript" class="w-8 h-8 object-contain">
            </div>
            <h3 class="text-2xl font-semibold text-white">abrw6</h3>
          </div>
          <p class="text-gray-400 mb-4">abrw6 is a hobby web browser made with Rust + gtk6 & gtk6WebView. Features a some what working adblocker, tabs and high customisablity of everything</p>
          <div class="flex gap-3">
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">Rust</span>
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">gtk6</span>
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">GtkWebView</span>
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">adblock-rust</span>
          </div>
        </a>

        <a target="blank" href="https://weather.aapelix.dev" class="p-6 rounded-2xl bg-[#111111] border border-[#222222] hover:border-[#A5B4FC]/20 transition-all duration-300 hover:scale-[1.02]">
          <div class="flex items-center gap-3 mb-4">
            <div class="w-12 h-12 rounded-xl bg-[#A5B4FC]/10 flex items-center justify-center">
              <img src="/ts.png" alt="typescript" class="w-8 h-8 object-contain">
            </div>
            <h3 class="text-2xl font-semibold text-white">weather.aapelix.dev</h3>
          </div>
          <p class="text-gray-400 mb-4">A simple weather app inspired by Nothing</p>
          <div class="flex gap-3">
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">Deno</span>
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">Fresh</span>
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">TailwindCSS</span>
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">Preact</span>
          </div>
        </a>

        <a target="blank" href="https://modrinth.com/mod/blocksmined" class="p-6 rounded-2xl bg-[#111111] border border-[#222222] hover:border-[#A5B4FC]/20 transition-all duration-300 hover:scale-[1.02]">
          <div class="flex items-center gap-3 mb-4">
            <div class="w-12 h-12 rounded-xl bg-[#A5B4FC]/10 flex items-center justify-center">
              <img src="/java.png" alt="typescript" class="w-8 h-8 object-contain">
            </div>
            <h3 class="text-2xl font-semibold text-white">BlocksMined</h3>
          </div>
          <p class="text-gray-400 mb-4">Fabric mod for Minecraft 1.21 that tracks each player's total blocks mined and allows score color customization</p>
          <div class="flex gap-3">
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">Java</span>
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">Fabric</span>
            <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">Minecraft</span>
          </div>
        </a>
      </div>
    </div>

    <div class="projects-section my-20">
      {#each repos.slice(0, limit) as repo}
        <div class="transform transition-all duration-300 hover:scale-[1.02] mb-4">
          <div class="p-6 rounded-2xl bg-[#111111] border border-[#222222] hover:border-[#A5B4FC]/20">
            <a href={repo.url} target="_blank" class="flex items-center gap-3 text-white">
              <Github class="text-[#A5B4FC]" />
              <h2 class="text-xl font-semibold">{repo.name}</h2>
            </a>
            <p class="mt-3 text-gray-400 text-sm">{repo.description}</p>
            <div class="flex gap-4 mt-4">
              {#if repo.language}
                <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-[#A5B4FC]">{repo.language}</span>
              {/if}
              <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-gray-400">{repo.stars} stars</span>
              {#if repo.fork}
                <span class="text-xs px-3 py-1 rounded-full bg-[#181818] text-gray-400">Forked</span>
              {/if}
            </div>
          </div>
        </div>
      {/each}
      
      <div class="flex justify-center gap-4 mt-8">
        <button 
          class="px-6 py-3 rounded-xl bg-[#111111] border border-[#222222] hover:border-[#A5B4FC]/20 transition-all duration-300"
          onclick={() => limit = limit + 5}
        >
          Show more
        </button>
        <button 
          class="px-6 py-3 rounded-xl bg-[#111111] border border-[#222222] hover:border-[#A5B4FC]/20 transition-all duration-300 disabled:opacity-50 disabled:cursor-not-allowed"
          onclick={() => limit = limit - 5}
          disabled={limit <= 5}
        >
          Show less
        </button>
      </div>
    </div>

    <div class="socials-section pb-20">
      <h2 class="text-5xl font-bold text-center mb-10 bg-gradient-to-r from-[#A5B4FC] to-[#818CF8] bg-clip-text text-transparent">
        My socials
      </h2>
      <div class="flex flex-wrap justify-center gap-4">
        <a 
          href="https://github.com/aapelix" 
          target="_blank" 
          class="social-link group flex items-center gap-3 px-6 py-4 rounded-xl bg-[#111111] border border-[#222222] hover:border-[#A5B4FC]/20 transition-all duration-300"
        >
          <Github class="text-[#A5B4FC] group-hover:scale-110 transition-transform duration-300" />
          <span>Github</span>
        </a>
        <a 
          href="https://youtube.com/@aapelix" 
          target="_blank" 
          class="social-link group flex items-center gap-3 px-6 py-4 rounded-xl bg-[#111111] border border-[#222222] hover:border-[#A5B4FC]/20 transition-all duration-300"
        >
          <Youtube class="text-[#A5B4FC] group-hover:scale-110 transition-transform duration-300" />
          <span>Youtube</span>
        </a>
        <a 
          href="https://x.com/@aapelix1" 
          target="_blank" 
          class="social-link group flex items-center gap-3 px-6 py-4 rounded-xl bg-[#111111] border border-[#222222] hover:border-[#A5B4FC]/20 transition-all duration-300"
        >
          <Twitter class="text-[#A5B4FC] group-hover:scale-110 transition-transform duration-300" />
          <span>Twitter</span>
        </a>
      </div>
    </div>
  </div>
</main>
    
    <style>
      :root {
    font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans', 'Helvetica Neue', sans-serif;
    color: #f0f0f0;
    background-color: #0f0f0f;
  }
    
  .slider-container {
    position: relative;
    height: 100px;
    margin: 0 auto; /* Centers the container */
  }
    
      .infinite-scroll {
        overflow: hidden;
        white-space: nowrap;
        width: 100%;
        height: 100%;
        position: relative;
      }
    
      .image-track {
        display: inline-flex;
        align-items: center;
        position: absolute;
        animation: scroll 40s linear infinite;
        will-change: transform;
        padding: 0 20px;
      }
    
      .image-track img {
        width: 100px;
        height: 100px; 
        margin-right: 20px; 
        object-fit: contain;
        padding: 10px;
        border-radius: 12px;
        transition: transform 0.3s ease, background 0.3s ease;
      }
    
      .image-track img:hover {
        transform: scale(1.1);
        filter: none;
      }
    
      .fade-overlay {
        position: absolute;
        top: 0;
        height: 100%;
        width: 150px;
        z-index: 10;
        pointer-events: none;
      }
    
      .fade-overlay.left {
    left: 0;
    background: linear-gradient(to right, 
      rgba(10, 10, 10, 1) 0%,
      rgba(10, 10, 10, 0.9) 40%,
      rgba(10, 10, 10, 0) 100%
    );
  }

  .fade-overlay.right {
    right: 0;
    background: linear-gradient(to left, 
    rgba(10, 10, 10, 1) 0%,
      rgba(10, 10, 10, 0.9) 40%,
      rgba(10, 10, 10, 0) 100%
    );
  }
    
      @keyframes scroll {
        0% {
          transform: translateX(0);
        }
        100% {
          transform: translateX(calc(-50% - 10px));
        }
      }
    
      .infinite-scroll:hover .image-track {
        animation-play-state: paused;
      }
    
      .image-track {
        backface-visibility: hidden;
        -webkit-backface-visibility: hidden;
        transform: translateZ(0);
        -webkit-transform: translateZ(0);
      }

      .tech-icon {
  filter: grayscale(1) sepia(1) hue-rotate(200deg) saturate(1.5) brightness(1.3);
  mix-blend-mode: lighten; /* Ensure white texture stands out */
}


    </style>