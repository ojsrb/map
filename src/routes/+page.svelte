<script lang="ts">
    import { MapLibre, NavigationControl, ScaleControl, GlobeControl, Marker, Popup, Terrain, Sky, RasterDEMTileSource, TerrainControl, Light, HillshadeLayer, GeolocateControl} from 'svelte-maplibre-gl';
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

    let groups: object[] = [
    ]

    function createGroup(name: String, color: String) {
        groups.push({
            name: name,
            color: color,
            displaying: true,
            points: []
        })
        groups = groups
    }

    let makingGroup = false

    let newGroup = {
        name: "",
        color: ""
    }

    function createPoint(group: String, name: String, lngLat) {
        groups.forEach((g) => {
            if (g.name === newPoint.group) {
                g.points.push({
                    name: newPoint.name,
                    lnglat: newPoint.lnglat,
                })
            }
        })
        groups = groups
    }

    function removePoint(group: String, name: String) {
        console.log("Removing", name)
        groups.forEach((g) => {
            if (g.name === group) {
                const index = g.points.findIndex((p) => p.name === group)
                g.points.splice(index, 1)
                g.points = g.points
            }
        })

        groups = groups
    }

    function removeGroup(name: String) {
        for (let i = 0; i < groups.length; i++) {
            if (groups[i].name === name) {
                groups.splice(i, 1)
                groups = groups
                break
            }
        }
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
    <div class="center flex flex-col">
        <h1 class="text-xl text-nowrap my-4">Owen's Map</h1>
        <hr class="w-2/3 h-1 mx-auto mb-4 bg-gray-100 border-0 rounded-sm dark:bg-gray-700">
    </div>
    {#each groups as group}
        <div class="rounded-lg bg-gray-100 m-4 p-4" onclick={() => {
            group.displaying = !group.displaying;
        }} ondblclick={() => {
            removeGroup(group.name)
        }}>
            <p style:color={group.color}>{group.name}</p>
        </div>
        {#if group.displaying}
            {#each group.points as point}
                <div class="rounded-lg bg-gray-100 m-4 p-4 ml-8 aria" onclick={() => {
                    center = point.lnglat
                }} ondblclick={() => {
                    removePoint(group.name, point.name)
                }}>
                    <p>{point.name}</p>
                </div>
            {/each}
        {/if}
    {/each}

    <div class="bottom-0 absolute flex flex-row align-middle" >
        <div class="rounded-lg bg-gray-100 m-4 p-4">
            {#if !makingGroup}
                <p onclick={() => {
                    makingGroup = true
                }}>Add Group</p>
            {/if}
            {#if makingGroup}
                <div class="flex flex-row">
                    <p class="m-1 w-16 ">Name:</p>
                    <input type="text" class="w-32 border-gray-400 border-1 rounded-lg" bind:value={newGroup.name} />
                </div>
                <div class="flex flex-row">
                    <p class="m-1 w-16">Color:</p>
                    <select bind:value={newGroup.color} class="w-32 border-gray-400 border-1 rounded-lg">
                        {#each colors as color}
                            <option value={color}>{color}</option>
                        {/each}
                    </select>
                </div>
                <button class="m-1 w-full text-blue-400 bg-gray-200 p-2 rounded-lg" onclick={() => {
                    createGroup(newGroup.name, newGroup.color)
                    groups = groups

                    makingGroup = false
                    console.log(groups)
                }}>Add</button>
            {/if}
        </div>
        {#if !makingGroup}
            <div class="m-4 ml-1 p-4 flex flex-col">
                Double click to add a point.
            </div>
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
    <Light anchor="map" />
    <RasterDEMTileSource
            id="terrain"
            tiles={['https://api.maptiler.com/tiles/terrain-rgb-v2/{z}/{x}/{y}.webp?key=CCQXFyWMI7rany1Z6XQP']}
            minzoom={0}
            maxzoom={12}
            attribution=""
    >
        <TerrainControl position="top-right" />
        <Terrain exaggeration={1.0}/>
    </RasterDEMTileSource>
    <!-- Hillshade -->
    <RasterDEMTileSource
            tiles={['https://api.maptiler.com/tiles/terrain-rgb-v2/{z}/{x}/{y}.webp?key=CCQXFyWMI7rany1Z6XQP']}
            minzoom={0}
            maxzoom={12}
            attribution='<a href="https://www.maptiler.com/copyright/" target="_blank">&copy; MapTiler</a> <a href="https://www.openstreetmap.org/copyright" target="_blank">&copy; OpenStreetMap contributors</a>'
    >
        <HillshadeLayer
                paint={{
        'hillshade-method': 'standard',
        'hillshade-exaggeration': 0.3,
        'hillshade-shadow-color': '#004050',
        'hillshade-accent-color': '#aaff00',
        'hillshade-highlight-color': '#ffffff',
        'hillshade-illumination-anchor': 'map',
        'hillshade-illumination-altitude': 45.0,
        'hillshade-illumination-direction': 0.0
      }}
        />
    </RasterDEMTileSource>
    <NavigationControl />
    <ScaleControl />
    <GlobeControl />
    <GeolocateControl />
    {#each groups as group}
        {#if group.displaying}
            {#each group.points as point}
                <Marker lnglat={point.lnglat} draggable={false} >
                    {#snippet content()}
                        <div class="text-center leading-none items-center" ondblclick={() => {
                            removePoint(group.name, point.name)
                        }}>
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
            createPoint(newPoint.group, newPoint.name, newPoint.lnglat)
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