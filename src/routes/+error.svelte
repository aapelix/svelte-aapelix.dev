<script lang="ts">
    import { page } from '$app/stores';
</script>

<div class="flex justify-center items-center flex-col w-screen h-screen">
    <h1 class="text-9xl font-black">{$page.status}</h1>
    <h1> {$page.error?.message}</h1>
</div>

<style>
    :root {
        color: #f0f0f0;
        background-color: #0f0f0f;
    }
</style>