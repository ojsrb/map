<script lang="ts">
    import { MapLibre, NavigationControl, ScaleControl, GlobeControl, Marker, Popup, Terrain} from 'svelte-maplibre-gl';
    import maplibregl from "maplibre-gl";

    const triangleright = "src/lib/triangle-right.svg"
    const triangledown = "src/lib/triangle-down.svg"
    const pin =  "src/lib/pin.svg"

    let popup = {
        enabled: false,
        lnglat: new maplibregl.LngLat(0, 0)
    }

    let newPoint = {
        name: "",
        lnglat: new maplibregl.LngLat(0, 0),
        group: ""
    }

    let center = new maplibregl.LngLat(-106.07, 39.47)

    const groups = [
        {
            name: "Bucket List",
            color: "#ff0000",
            displaying: true,
            points: [
                {
                    name: "test",
                    lnglat: new  maplibregl.LngLat(0, 0)
                },
            ]
        }
    ]
</script>

{#snippet label(name)}
    <p>{name}</p>
{/snippet}
<div class="flex flex-row">
<div class="w-1/6">
    {#each groups as group}
        <button class="bg-gray-100 m-5 rounded-lg p-3 flex flex-row w-72" on:click={() => {group.displaying= !group.displaying}}>
            <p style="color: {group.color}" class="mr-[9rem]">{group.name}</p>
            {#if group.displaying === true}
                <svg width="800px" height="800px" viewBox="0 -0.5 17 17" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" class="si-glyph si-glyph-triangle-down">

                    <title>1237</title>

                    <defs>

                    </defs>
                    <g stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                        <path d="M10.106,12.69 C9.525,13.27 8.584,13.27 8.002,12.69 L1.561,6.246 C0.979,5.665 0.722,4.143 2.561,4.143 L15.549,4.143 C17.45,4.143 17.131,5.664 16.549,6.246 L10.106,12.69 L10.106,12.69 Z" fill="#434343" class="si-glyph-fill">

                        </path>
                    </g>
                </svg>
            {/if}
            {#if group.displaying === false}
                <svg width="800px" height="800px" viewBox="0 -0.5 17 17" version="1.1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" class="si-glyph si-glyph-triangle-right">

                <title>1234</title>

                <defs>

                </defs>
                <g stroke="none" stroke-width="1" fill="none" fill-rule="evenodd">
                    <path d="M6.113,15.495 C5.531,16.076 4.01,16.395 4.01,14.494 L4.01,1.506 C4.01,-0.333 5.531,-0.076 6.113,0.506 L12.557,6.948 C13.137,7.529 13.137,8.47 12.557,9.052 L6.113,15.495 L6.113,15.495 Z" fill="#434343" class="si-glyph-fill">

                    </path>
                </g>
                </svg>
        </button>
        {#if group.displaying}
            <div>
                {#each group.points as point}
                    <div class="bg-gray-100 m-5 rounded-xl w-72 p-3 flex flex-row ml-16">
                        <button class="" on:click={() => {
                            center = point.lnglat
                        }}>
                            <p class="">{point.name}</p>
                        </button>
                        <button aria-label="delete">
                            <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg"><g id="SVGRepo_bgCarrier" stroke-width="0"></g><g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g><g id="SVGRepo_iconCarrier"> <path d="M10 12L14 16M14 12L10 16M4 6H20M16 6L15.7294 5.18807C15.4671 4.40125 15.3359 4.00784 15.0927 3.71698C14.8779 3.46013 14.6021 3.26132 14.2905 3.13878C13.9376 3 13.523 3 12.6936 3H11.3064C10.477 3 10.0624 3 9.70951 3.13878C9.39792 3.26132 9.12208 3.46013 8.90729 3.71698C8.66405 4.00784 8.53292 4.40125 8.27064 5.18807L8 6M18 6V16.2C18 17.8802 18 18.7202 17.673 19.362C17.3854 19.9265 16.9265 20.3854 16.362 20.673C15.7202 21 14.8802 21 13.2 21H10.8C9.11984 21 8.27976 21 7.63803 20.673C7.07354 20.3854 6.6146 19.9265 6.32698 19.362C6 18.7202 6 17.8802 6 16.2V6" stroke="#000000" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"></path> </g></svg>
                        </button>
                    </div>
                {/each}
            </div>
        {/if}
    {/each}
</div>

<MapLibre
        class="h-[100vh] min-h-[300px] w-5/6 right-0"
        style="https://basemaps.cartocdn.com/gl/voyager-gl-style/style.json"
        zoom={5}
        bind:center={center}
        doubleClickZoom={false}
        ondblclick={(e) => {
            if (!popup.enabled) {
                popup.enabled = true;
                popup.lnglat = e.lngLat;
                newPoint.lnglat = e.lngLat;
            }

        }}
>
    <NavigationControl />
    <ScaleControl />
    <GlobeControl />
    {#each groups as group}
        {#if group.displaying}
            {#each group.points as point}
                <Marker lnglat={point.lnglat} draggable={false} >
                    {#snippet content()}
                        <div class="text-center leading-none items-center">
                            <svg width="4rem" height="4rem" viewBox="0 0 24 24" fill={group.color} xmlns="http://www.w3.org/2000/svg">
                                <path fill-rule="evenodd" clip-rule="evenodd" d="M12.398 19.804C13.881 19.0348 19 16.0163 19 11C19 7.13401 15.866 4 12 4C8.13401 4 5 7.13401 5 11C5 16.0163 10.119 19.0348 11.602 19.804C11.8548 19.9351 12.1452 19.9351 12.398 19.804ZM12 14C13.6569 14 15 12.6569 15 11C15 9.34315 13.6569 8 12 8C10.3431 8 9 9.34315 9 11C9 12.6569 10.3431 14 12 14Z" fill={group.color} />
                            </svg>
                            <p>{point.name}</p>
                        </div>
                    {/snippet}
                </Marker>
            {/each}
        {/if}
    {/each}
    <Popup lnglat={popup.lnglat} bind:open={popup.enabled} onclose={() => {
        if (newPoint.name !== "" && newPoint.group !== "") {
            popup.enabled = false
            for (let i = 0; i < groups.length; i++) {
                if (groups[i].name === newPoint.group) {
                    groups[i].points.push({
                        name: newPoint.name,
                        lnglat: newPoint.lnglat,
                    })
                    groups[i].points = groups[i].points
                }
            }
            newPoint.name = ""
            newPoint.lnglat = new  maplibregl.LngLat(0, 0)
            newPoint.group = ""
        }
    }}>
        Name:
        <input type="text" bind:value={newPoint.name} style="m-1"/>
        Group:
        <select bind:value={newPoint.group} style="m-1">
            {#each groups as group}
                <option value={group.name}>{group.name}</option>
            {/each}
        </select>
        <button class="bg-blue-500 rounded-full p-1 px-2 m-1" on:click={() => popup.enabled = false}>
            Done
        </button>
    </Popup>

</MapLibre>
</div>