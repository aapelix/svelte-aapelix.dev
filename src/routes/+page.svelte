<script>
    import { error } from "@sveltejs/kit";
    import { onMount } from "svelte";

    import { Github, Youtube, Twitter } from "lucide-svelte"

    /**
	 * @type {any[]}
	 */
    let repos = [];

    let limit = 5;
    
    export function load() {
      throw error(404, "Not found");
    }
    
    const quotes = ["flew to the moon", "launched a nuclear missile to mars", "ate uranium", "bought a fighter jet", "installed Arch Linux"];
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
    
    <main class="flex justify-center items-center pb-5">
      <div class="md:w-2/3 w-full">
        <header class="text-center flex flex-col justify-center h-screen relative">
          <div 
            class="absolute top-1/2 transform -translate-y-1/2 w-full md:h-2/3 h-full md:scale-125 rounded-xl bg-cover bg-center opacity-50 mx-auto"
            style="background-image: url('/g.png');">
          </div>
          <div class="relative z-10">
            <h1 class="md:text-9xl text-7xl font-black">"It's me, aapelix"</h1>
            <p class="mt-5 text-2xl">he said and {quote}</p>
          </div>
        </header>
        <div class="slider-container">
          <div class="fade-overlay left"></div>
          <div class="infinite-scroll">
            <div class="image-track" bind:this={imageTrack}>
              <img src="/rust.png" alt="rust">
              <img src="/csharp.png" alt="csharp">
              <img src="/astro.png" alt="astro">
              <img src="/go.png" alt="go">
              <img src="/java.png" alt="java">
              <img src="/svelte.png" alt="svelte">
              <img src="/cpp.png" alt="cpp">
              <img src="/python.png" alt="python">
              <img src="/js.png" alt="js">
              <img src="/vue.png" alt="vue">
              <img src="/c.png" alt="c">
              <img src="/react.png" alt="react">
              <img src="/ts.png" alt="ts">
            </div>
          </div>
          <div class="fade-overlay right"></div>
        </div>

        <div class="mt-10 px-2">
            {#each repos.slice(0, limit) as repo}
                <div class="flex flex-col gap-2 p-4 rounded-xl bg-[#181818] w-full mt-2">
                    <a href={repo.url} target="_blank" class="flex items-center gap-2 text-white">
                        <Github />
                        <h1 class="text-2xl font-bold">{repo.name}</h1>
                    </a>
                    <p class="text-sm text-white">{repo.description}</p>
                    <div class="flex gap-2 mt-2">
                        <p class="text-xs text-white">{repo.language}</p>
                        <p class="text-xs text-white">{repo.stars} stars</p>
                        {#if repo.fork}
                            <p class="text-xs text-white">Forked</p>
                        {/if}
                    </div>
                </div>
            {/each}
            
            <div class="flex mt-2 gap-2">
                <button class="rounded-xl bg-[#222222] px-4 w-44 py-4" onclick={() => limit = limit + 5}>Show more</button>
                <button class="rounded-xl bg-[#222222] disabled:bg-[#181818] px-4 w-44 py-4 disabled:cursor-not-allowed" onclick={() => limit = limit - 5} disabled={limit <= 5}>Show less</button>
            </div>
        </div>

        <div class="flex items-center flex-col mt-10 px-2">
            <h1 class="text-6xl font-bold text-center">Explore my socials</h1>
            <div class="mt-5 flex gap-2 flex-wrap justify-center items-center">
                <a href="https://github.com/aapelix" target="_blank" class="rounded-xl bg-[#222222] px-4 w-44 py-4 gap-2 text-white flex items-center justify-center hover:w-96 duration-300"><Github />Github</a>
                <a href="https://youtube.com/@aapelix" target="_blank" class="rounded-xl bg-[#181818] px-4 w-44 py-4 gap-2 text-white flex items-center justify-center hover:w-96 duration-300"><Youtube />Youtube</a>
                <a href="https://x.com/@aapelix1" target="_blank" class="rounded-xl bg-[#181818] px-4 w-44 py-4 gap-2 text-white flex items-center justify-center hover:w-96 duration-300"><Twitter />Twitter</a>
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
        width: 100%;
        height: 100px;
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
        background: rgba(255, 255, 255, 0.1);
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
          rgba(15, 15, 15, 1) 0%,
          rgba(15, 15, 15, 0.9) 40%,
          rgba(15, 15, 15, 0) 100%
        );
      }
    
      .fade-overlay.right {
        right: 0;
        background: linear-gradient(to left, 
          rgba(15, 15, 15, 1) 0%,
          rgba(15, 15, 15, 0.9) 40%,
          rgba(15, 15, 15, 0) 100%
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
    </style>