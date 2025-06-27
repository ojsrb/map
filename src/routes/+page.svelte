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

    let groups = [
        {
            name: "Bucket List",
            color: "#ff0000",
            displaying: true,
            points: [
                {
                    name: "test",
                    lnglat: new  maplibregl.LngLat(0, 0)
                },
                {
                    name: "test",
                    lnglat: new  maplibregl.LngLat(0, 0)
                }
            ]
        }
    ]

    let makingGroup = false

    let newGroup = {
        name: "",
        color: "",
        displaying: true,
        points: []
    }

    const colors = [
        "red",
        "green",
        "blue",
        "yellow",
        "purple",
        "pink",
        "orange",
    ]

</script>

<div class="flex flex-row">
<div class="w-1/3 lg:w-1/6">
    {#each groups as group}
        <div class="rounded-lg bg-gray-100 m-4 p-4" onclick={() => {
            group.displaying = !group.displaying;
        }}>
            <p style:color={group.color}>{group.name}</p>
        </div>
        {#if group.displaying}
            {#each group.points as point}
                <div class="rounded-lg bg-gray-100 m-4 p-4 ml-8 aria" onclick={() => {
                    center = point.lnglat
                }}>
                    <p>{point.name}</p>
                </div>
            {/each}
        {/if}
    {/each}

    <div class="bottom-0 absolute rounded-lg bg-gray-100 m-4 p-4" >
        {#if !makingGroup}
            <p onclick={() => {
                makingGroup = true
            }}>Add Group</p>
        {/if}
        {#if makingGroup}
            <div class="flex flex-row">
                <p class="m-1 w-16 ">Name:</p>
                <input type="text" class="w-32 border-gray-400 border-1" bind:value={newGroup.name} />
            </div>
            <div class="flex flex-row">
                <p class="m-1 w-16">Color:</p>
                <select bind:value={newGroup.color} class="w-32 border-gray-400 border-1">
                    {#each colors as color}
                        <option value={color}>{color}</option>
                    {/each}
                </select>
            </div>
            <button class="m-1 w-full text-blue-400 bg-gray-200 p-2 rounded-lg" onclick={() => {
                groups.push(newGroup)
                groups = groups
                makingGroup = false
            }}>Add</button>
        {/if}
    </div>
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
    <Popup class="p-2" lnglat={popup.lnglat} bind:open={popup.enabled} onclose={() => {
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
        <div class="flex flex-row">
            <p class="m-1 w-16 ">Name:</p>
            <input type="text" class="w-32 border-gray-400 border-1" bind:value={newPoint.name} />
        </div>
        <div class="flex flex-row">
            <p class="m-1 w-16">Group:</p>
            <select bind:value={newPoint.group} class="w-32 border-gray-400 border-1">
                {#each groups as group}
                    <option value={group.name}>{group.name}</option>
                {/each}
            </select>
        </div>
        <button class="m-1 w-full text-blue-400 bg-gray-200 p-2 rounded-lg" onclick={() => {
                popup.enabled = false
            }}>Add</button>
    </Popup>

</MapLibre>
</div>