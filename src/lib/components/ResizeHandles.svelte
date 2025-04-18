<script lang="ts">
    import { platform } from '@tauri-apps/plugin-os';
    import { onMount } from 'svelte';
    import { getCurrentWindow } from '@tauri-apps/api/window';

    type ResizeDirection = 'East' | 'North' | 'NorthEast' | 'NorthWest' | 'South' | 'SouthEast' | 'SouthWest' | 'West';

    interface ResizeHandle {
        direction: ResizeDirection;
        position: string;
        size: string;
        cursor: string;
    }

    const resizeHandles: ResizeHandle[] = [
        { direction: 'NorthWest', position: 'top-0 left-0', size: 'w-4 h-4', cursor: 'cursor-nw-resize' },
        { direction: 'NorthEast', position: 'top-0 right-0', size: 'w-4 h-4', cursor: 'cursor-ne-resize' },
        { direction: 'SouthWest', position: 'bottom-0 left-0', size: 'w-4 h-4', cursor: 'cursor-sw-resize' },
        { direction: 'SouthEast', position: 'bottom-0 right-0', size: 'w-4 h-4', cursor: 'cursor-se-resize' },
        { direction: 'North', position: 'top-0 left-4 right-4', size: 'h-2', cursor: 'cursor-n-resize' },
        { direction: 'East', position: 'top-4 right-0 bottom-4', size: 'w-2', cursor: 'cursor-e-resize' },
        { direction: 'South', position: 'bottom-0 left-4 right-4', size: 'h-2', cursor: 'cursor-s-resize' },
        { direction: 'West', position: 'top-4 left-0 bottom-4', size: 'w-2', cursor: 'cursor-w-resize' },
    ];

    const appWindow = getCurrentWindow();
    let isLinux = false;

    onMount(async () => {
        const platformName = await platform();
        isLinux = platformName === 'linux';
    });

    async function startResize(event: MouseEvent, direction: ResizeDirection) {
        event.preventDefault();
        appWindow.startResizeDragging(direction);
    }
</script>

{#if isLinux}
<div class="fixed inset-0 pointer-events-none z-[9999]">
    {#each resizeHandles as handle}
        <div 
            class="absolute {handle.position} {handle.size} {handle.cursor} pointer-events-auto"
            on:mousedown={(e) => startResize(e, handle.direction)}
            role="presentation"
            tabindex="-1"
        ></div>
    {/each}
</div>
{/if}
