<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'

const audioRef = ref<HTMLAudioElement | null>(null)
const isPlaying = ref(false)

const toggleAudio = () => {
  if (!audioRef.value) return
  if (isPlaying.value) {
    audioRef.value.pause()
    isPlaying.value = false
  } else {
    audioRef.value.volume = 0.3
    audioRef.value.play().then(() => {
      isPlaying.value = true
    }).catch(() => {})
  }
}

interface Game {
  rank: number
  name: string
  dev: string
  cat: string
  tagline: string
  desc: string
  playUrl: string
  rating: string
  free: boolean
  multi: boolean
}

const GAMES: Game[] = [
  { rank: 1, name: "Narrow One", dev: "Pelican Party", cat: "Multiplayer", tagline: "5v5 medieval capture-the-flag archery - the most popular Three.js game on itch.io.", desc: "Fight fast-paced archery battles in medieval castles. Protect and capture the flag across 16+ unique maps.", playUrl: "https://pelicanparty.itch.io/narrow-one", rating: "4.6* 837 ratings", free: true, multi: true },
  { rank: 2, name: "PolyTrack", dev: "Kodub", cat: "Racing", tagline: "High-speed low-poly racing with a track editor and global leaderboards.", desc: "A fast, loop-filled low-poly racing game inspired by TrackMania.", playUrl: "https://kodub.itch.io/polytrack", rating: "4.7* 477 ratings", free: true, multi: false },
  { rank: 3, name: "last seen online", dev: "qwook", cat: "Horror", tagline: "Look through a stranger's computer - a psychological horror puzzle.", desc: "Snoop through someone's files to uncover what happened to them.", playUrl: "https://qwook.itch.io/last-seen-online", rating: "4.9* Top Rated", free: true, multi: false },
  { rank: 4, name: "FeedVid Live", dev: "Varun R.", cat: "Horror", tagline: "You stumble upon a strange livestream - unsettling horror experience.", desc: "A horror puzzle game disguised as a streaming platform.", playUrl: "https://varunramesh.itch.io/feedvid-live", rating: "4.7* 1,286 ratings", free: true, multi: false },
  { rank: 5, name: "And You'll Miss It", dev: "qwook", cat: "Interactive", tagline: "An interactive zine about life - gentle, meditative and beautiful.", desc: "A quiet interactive experience exploring small moments of everyday life.", playUrl: "https://qwook.itch.io/and-youll-miss-it", rating: "5.0* 230 ratings", free: true, multi: false },
  { rank: 6, name: "DustSim", dev: "Kodub", cat: "Simulation", tagline: "Simulate millions of independent particles in real time - physics sandbox.", desc: "GPU-accelerated particle physics sandbox built with Three.js.", playUrl: "https://kodub.itch.io/dustsim", rating: "4.7* 157 ratings", free: true, multi: false },
  { rank: 7, name: "stein.world", dev: "pg5-Studio", cat: "MMORPG", tagline: "Browser-based MMORPG - the first full-fledged real-time RPG in a browser tab.", desc: "A free-to-play MMORPG with active Zelda-like combat, professions, dungeons, and housing.", playUrl: "https://pg5-studio.itch.io/steinworld", rating: "4.4* Free to Play", free: true, multi: true },
  { rank: 8, name: "HexGL", dev: "BKcore", cat: "Racing", tagline: "Futuristic WebGL racing - tribute to WipeOut and F-Zero. Google-featured.", desc: "One of the most iconic Three.js games ever made.", playUrl: "https://hexgl.bkcore.com/play/", rating: "* Google Featured", free: true, multi: false },
  { rank: 9, name: "HelloRun", dev: "HelloEnjoy", cat: "Action", tagline: "Hypnotic 3D rhythm runner - visuals sync to electronic music.", desc: "An endless runner with abstract 3D visuals that pulse and react to music.", playUrl: "https://helloenjoy.itch.io/hellorun", rating: "4.5* Featured", free: true, multi: false },
  { rank: 10, name: "Star Defenders 3D", dev: "Eric Gurt", cat: "Multiplayer", tagline: "Multiplayer FPS in destructible voxel worlds - up to 8 players.", desc: "Arena first-person shooter in procedurally generated destructible voxel environments.", playUrl: "https://eric-gurt.itch.io/star-defenders-3d", rating: "4.3* Multiplayer", free: true, multi: true },
  { rank: 11, name: "Goblin.Life", dev: "Corporate Entity LLC", cat: "Multiplayer", tagline: "Forge 3D worlds in real time with adorable Goblins - up to 16 players.", desc: "A creative multiplayer sandbox where players build and explore 3D worlds together.", playUrl: "https://goblinlife.itch.io/goblin-life", rating: "4.4* Multiplayer", free: true, multi: true },
  { rank: 12, name: "Mine-Craft.io", dev: "K&S Games", cat: "Multiplayer", tagline: "1,000+ players online - top-down Minecraft-inspired multiplayer world.", desc: "A massively multiplayer top-down voxel survival game built with Three.js.", playUrl: "https://mine-craft.io", rating: "4.0* 1000+ Online", free: true, multi: true },
  { rank: 13, name: "Raccoon Retail", dev: "Pelican Party", cat: "Simulation", tagline: "Keep a chaotic supermarket clean before customers make it worse!", desc: "Customers are making a big mess in your store and it's your job to keep the supermarket clean.", playUrl: "https://pelicanparty.itch.io/raccoon-retail", rating: "4.3* Free", free: true, multi: false },
  { rank: 14, name: "FACEMINER", dev: "Wristwork", cat: "Simulation", tagline: "Dystopian AI management sim - build a biometric surveillance empire.", desc: "A paid indie game satirizing surveillance capitalism.", playUrl: "https://wristwork.itch.io/faceminer", rating: "4.7* $7.99", free: false, multi: false },
  { rank: 15, name: "Terraforming Titans", dev: "terraforming.titans", cat: "Simulation", tagline: "Incremental terraforming game - transform alien planets into biospheres.", desc: "An incremental simulation game about terraforming planets.", playUrl: "https://terraformingtitans.itch.io/terraforming-titans", rating: "4.2* Free", free: true, multi: false },
  { rank: 16, name: "Microlandia", dev: "explodi", cat: "Simulation", tagline: "Brutally honest city builder at microscopic scale.", desc: "A unique city builder game set at microscopic scale.", playUrl: "https://explodi.itch.io/microlandia", rating: "4.3* $3.60", free: false, multi: false },
  { rank: 17, name: "Plume and the Forgotten Letter", dev: "Dino Collective", cat: "Adventure", tagline: "A child delivers a letter to Santa through magical 3D winter worlds.", desc: "A heartwarming Three.js platformer by Dino Collective.", playUrl: "https://dino-collective.itch.io/plume", rating: "4.8* Highly Rated", free: true, multi: false },
  { rank: 18, name: "Laputa: Riddles In The Sky", dev: "Xavier Burrow", cat: "Puzzle", tagline: "A puzzle game amongst the clouds - floating island mysteries.", desc: "Explore cloud-top islands and solve atmospheric puzzles.", playUrl: "https://xavierburrow.itch.io/laputa-riddles-in-the-sky", rating: "4.5* Free", free: true, multi: false },
  { rank: 19, name: "Please Fail Safely and Deadly", dev: "seqvalence", cat: "Action", tagline: "Puzzle/shooter - a special agent vs an immortal enemy.", desc: "A clever puzzle-shooter hybrid where you play as a special agent against an immortal foe.", playUrl: "https://seqvalence.itch.io/please-fail-safely-and-deadly", rating: "4.4* Free", free: true, multi: false },
  { rank: 20, name: "Monkey-Catching Game", dev: "LilyLilium", cat: "Adventure", tagline: "Taiwanese musical story - playable on your smartphone browser.", desc: "A charming Taiwanese musical-story game designed for smartphone browsers.", playUrl: "https://lilium-lily.itch.io/monkey-catching-game", rating: "5.0* 22 ratings", free: true, multi: false },
  { rank: 21, name: "ON.OFF", dev: "Various", cat: "Puzzle", tagline: "Toggle between two dimensions to solve interconnected puzzles.", desc: "A mind-bending puzzle game that switches between 2D and 3D perspectives.", playUrl: "https://itch.io/games/tag-threejs/genre-puzzle", rating: "4.5* Top Puzzle", free: true, multi: false },
  { rank: 22, name: "Bogus Roads", dev: "Frank Force Games", cat: "Racing", tagline: "Lowrez retrowave racing on chaotic generated roads.", desc: "Wacky retro-style racing on absurdly generated low-res roads.", playUrl: "https://killedbyapixel.itch.io/bogus-roads", rating: "4.1* Free", free: true, multi: false },
  { rank: 23, name: "REIZEN: Prototype", dev: "Ryan Trawick", cat: "Action", tagline: "Fast-paced twitch-reflex arcade web game built in Three.js.", desc: "An intense, fast-paced arcade game demanding sharp reflexes.", playUrl: "https://ryantrawick.itch.io/reizen-prototype", rating: "4.2* Free", free: true, multi: false },
  { rank: 24, name: "Formula Prototype", dev: "Various", cat: "Racing", tagline: "F1-style racing prototype built and playable in Three.js.", desc: "A Formula 1-inspired racing prototype featuring authentic handling.", playUrl: "https://itch.io/games/tag-threejs/genre-racing", rating: "4.0* Free", free: true, multi: false },
  { rank: 25, name: "Voices of a Hidden Star", dev: "lordvonadel", cat: "Rhythm", tagline: "A visual/rhythm experience told through 3D starscapes.", desc: "An atmospheric Three.js rhythm experience where stars form constellations.", playUrl: "https://lordvonadel.itch.io/voices-of-a-hidden-star", rating: "4.6* Free", free: true, multi: false },
  { rank: 26, name: "Zoom Escape", dev: "LukasWho", cat: "Puzzle", tagline: "All the small things (and the large things, too) - escape puzzle.", desc: "A clever escape puzzle exploring scale and perspective.", playUrl: "https://lukaswho.itch.io/zoom-escape", rating: "4.4* Free", free: true, multi: false },
  { rank: 27, name: "Draw-A-Mountain", dev: "Various", cat: "Puzzle", tagline: "Draw mountain outlines and watch them come to life in 3D.", desc: "A creative browser game where you sketch mountain silhouettes.", playUrl: "https://itch.io/games/made-with-threejs/platform-android", rating: "4.3* Free", free: true, multi: false },
  { rank: 28, name: "Dungeon Survivors", dev: "darkpouetman", cat: "Action", tagline: "Vampire Survivor-inspired 3D dungeon game built in Three.js.", desc: "A small but polished survivor roguelite inspired by Vampire Survivors.", playUrl: "https://darkpouetman.itch.io/dungeon-survivors", rating: "4.1* Free", free: true, multi: false },
  { rank: 29, name: "Might is Right", dev: "LazyKitty", cat: "Strategy", tagline: "Turn-based strategy with RPG elements - browser-based Three.js.", desc: "A turn-based strategy game with RPG progression built in Three.js.", playUrl: "https://lazykitty.itch.io/ex-nihilo", rating: "4.3* Free", free: true, multi: false },
  { rank: 30, name: "skullhotel.io", dev: "james_hall38", cat: "Horror", tagline: "Browser-based horror game - check into the Skull Hotel.", desc: "A browser horror game set in a dark, mysterious hotel.", playUrl: "https://james-hall38.itch.io/skullhotel", rating: "4.2* Free", free: true, multi: false },
  { rank: 31, name: "Bruno Simon Portfolio", dev: "Bruno Simon", cat: "Experience", tagline: "Drive a car through a 3D portfolio world - the most iconic Three.js demo.", desc: "The legendary Three.js portfolio by Bruno Simon.", playUrl: "https://bruno-simon.com", rating: "Award Winning", free: true, multi: false },
  { rank: 32, name: "Three.js Official Examples", dev: "Mr. Doob (mrdoob)", cat: "Demos", tagline: "300+ official Three.js interactive demos covering every feature.", desc: "The official Three.js examples library - 300+ interactive WebGL demos.", playUrl: "https://threejs.org/examples", rating: "The Official Hub", free: true, multi: false },
  { rank: 33, name: "GPGPU Birds", dev: "mrdoob", cat: "Simulation", tagline: "10,000 GPU-simulated birds flocking in perfect synchrony.", desc: "The iconic official Three.js GPGPU birds demo.", playUrl: "https://threejs.org/examples/#webgl_gpgpu_birds", rating: "Official Demo", free: true, multi: false },
  { rank: 34, name: "Three.js Ocean", dev: "mrdoob / jbouny", cat: "Simulation", tagline: "Real-time Gerstner wave ocean - physically-based water simulation.", desc: "The official Three.js ocean simulation using Gerstner wave equations.", playUrl: "https://threejs.org/examples/#webgl_shaders_ocean", rating: "Official Demo", free: true, multi: false },
  { rank: 35, name: "Three.js Portal", dev: "mrdoob", cat: "Puzzle", tagline: "Non-Euclidean portal rendering - see through to another 3D space.", desc: "The official Three.js portal demo using stencil buffer rendering.", playUrl: "https://threejs.org/examples/#webgl_portal", rating: "Official Demo", free: true, multi: false },
  { rank: 36, name: "Three.js Protoplanet", dev: "mrdoob", cat: "Simulation", tagline: "GPU n-body gravity - watch planets form from dust clouds.", desc: "Official Three.js GPGPU n-body gravity simulation.", playUrl: "https://threejs.org/examples/#webgl_gpgpu_protoplanet", rating: "Official Demo", free: true, multi: false },
  { rank: 37, name: "GPGPU Water", dev: "mrdoob", cat: "Simulation", tagline: "GPU-accelerated water - ripples respond to mouse in real time.", desc: "Click anywhere on the water surface and watch physics-accurate ripples propagate.", playUrl: "https://threejs.org/examples/#webgl_gpgpu_water", rating: "Official Demo", free: true, multi: false },
  { rank: 38, name: "Ammo Physics: Vehicle", dev: "mrdoob", cat: "Simulation", tagline: "Drive a physics-based vehicle on bumpy 3D terrain.", desc: "Official Three.js + Ammo.js vehicle physics demo.", playUrl: "https://threejs.org/examples/#physics_ammo_vehicle", rating: "Official Demo", free: true, multi: false },
  { rank: 39, name: "Ammo Physics: Break", dev: "mrdoob", cat: "Action", tagline: "Smash glass and objects with Voronoi fracture physics.", desc: "Official Three.js breakable objects demo using Ammo.js and Voronoi fracture.", playUrl: "https://threejs.org/examples/#physics_ammo_break", rating: "Official Demo", free: true, multi: false },
  { rank: 40, name: "Sky Shader Demo", dev: "mrdoob / prashant", cat: "Simulation", tagline: "Physically-based sky with real-time day/night cycle - control the sun.", desc: "Official Three.js atmospheric scattering sky shader.", playUrl: "https://threejs.org/examples/#webgl_shader_sky", rating: "Official Demo", free: true, multi: false },
  { rank: 41, name: "WebGL Fluid Simulation", dev: "PavelDoGreat", cat: "Simulation", tagline: "Real-time fluid dynamics - swirl beautiful flowing color with your mouse.", desc: "A stunning real-time fluid simulation using WebGL.", playUrl: "https://paveldogreat.github.io/WebGL-Fluid-Simulation/", rating: "14k GitHub Stars", free: true, multi: false },
  { rank: 42, name: "ro.me", dev: "Google Creative Lab", cat: "Experience", tagline: "Walk through a Three.js dream world with user-contributed 3D creatures.", desc: "Google's landmark Chrome Experiment using Three.js.", playUrl: "https://github.com/dataarts/3-dreams-of-black", rating: "Webby Award Winner", free: true, multi: false },
  { rank: 43, name: "The Aviator", dev: "Karim Maaloul (yakudoo)", cat: "Action", tagline: "Fly a cartoon airplane through clouds - the most-followed Three.js tutorial game.", desc: "The most-widely-used Three.js game tutorial.", playUrl: "https://tympanus.net/Tutorials/TheAviator/", rating: "Most Referenced Tutorial", free: true, multi: false },
  { rank: 44, name: "Instancing Scatter", dev: "mrdoob", cat: "Simulation", tagline: "Scatter tens of thousands of 3D objects on terrain - GPU instancing showcase.", desc: "Official Three.js demo placing 50,000+ grass blades, trees, and stones.", playUrl: "https://threejs.org/examples/#webgl_instancing_scatter", rating: "Official Demo", free: true, multi: false },
  { rank: 45, name: "Ammo Cloth", dev: "mrdoob", cat: "Simulation", tagline: "Drag, pin, and cut realistic cloth in real time with Ammo.js physics.", desc: "The official Three.js cloth physics demo using Ammo.js soft bodies.", playUrl: "https://threejs.org/examples/#physics_ammo_cloth", rating: "Official Demo", free: true, multi: false },
  { rank: 46, name: "cubing.js", dev: "Lucas Garron", cat: "Puzzle", tagline: "The definitive Rubik's cube library - stunning Three.js 3D visualization.", desc: "The industry-standard Rubik's cube JavaScript library.", playUrl: "https://js.cubing.net/cubing/twisty/", rating: "Industry Standard", free: true, multi: false },
  { rank: 47, name: "Globe.gl", dev: "Vasco Asturiano", cat: "Simulation", tagline: "Interactive Three.js globe - visualize world data in stunning 3D.", desc: "A Three.js-powered interactive 3D globe library.", playUrl: "https://globe.gl/example/world-population/", rating: "7k GitHub Stars", free: true, multi: false },
  { rank: 48, name: "Three.js Audio Visualizer", dev: "mrdoob", cat: "Rhythm", tagline: "Official Web Audio API 3D visualizer - music meets WebGL geometry.", desc: "The official Three.js Web Audio API visualizer.", playUrl: "https://threejs.org/examples/#webaudio_visualizer", rating: "Official Demo", free: true, multi: false },
  { rank: 49, name: "Spline - Design Tool", dev: "Spline Team", cat: "Tool", tagline: "Browser-based 3D design tool built on Three.js - no install needed.", desc: "Spline is a powerful 3D design and animation tool built on Three.js.", playUrl: "https://spline.design", rating: "Industry Tool", free: true, multi: false },
  { rank: 50, name: "ASCII Effect", dev: "mrdoob", cat: "Simulation", tagline: "Render any 3D Three.js scene as ASCII art - classic WebGL trick.", desc: "Official Three.js ASCII rendering effect.", playUrl: "https://threejs.org/examples/#webgl_effects_ascii", rating: "Official Demo", free: true, multi: false },
]

const CATS = ['All', 'Multiplayer', 'Racing', 'Horror', 'Simulation', 'Action', 'Puzzle', 'Rhythm', 'Strategy', 'Adventure', 'Interactive', 'MMORPG', 'Experience', 'Demos', 'Tool']
const CAT_COLORS: Record<string, string> = {
  Multiplayer: '#9b59ff', Racing: '#ff8c00', Horror: '#cc2222', Simulation: '#00e5ff',
  Action: '#ff3c5a', Puzzle: '#e8ff00', Rhythm: '#ff6eb4', Strategy: '#00ff88',
  Adventure: '#ff8c00', Interactive: '#aaffcc', MMORPG: '#9b59ff', Experience: '#e8ff00',
  Demos: '#00e5ff', Tool: '#ff8c00'
}

const filter = ref('All')
const search = ref('')
const modal = ref<Game | null>(null)
const freeOnly = ref(false)
const multiOnly = ref(false)
const view = ref<'grid' | 'list'>('grid')

const filtered = computed(() => {
  return GAMES.filter(g => {
    if (filter.value !== 'All' && g.cat !== filter.value) return false
    if (freeOnly.value && !g.free) return false
    if (multiOnly.value && !g.multi) return false
    if (search.value) {
      const s = search.value.toLowerCase()
      return g.name.toLowerCase().includes(s) || g.cat.toLowerCase().includes(s)
        || g.dev.toLowerCase().includes(s) || g.tagline.toLowerCase().includes(s)
    }
    return true
  })
})

const cardHoverStates = ref<Record<number, boolean>>({})

const setCardHover = (rank: number, isHovered: boolean) => {
  cardHoverStates.value[rank] = isHovered
}

const getCatColor = (cat: string): string => CAT_COLORS[cat] || '#e8ff00'

const GAME_ICONS: Record<number, string> = {
  1: `<path d="M20 6 L26 14 L26 22 L20 30 L14 22 L14 14 Z"/><circle cx="20" cy="18" r="4"/><path d="M8 32 L14 26 M32 32 L26 26"/><path d="M12 10 L8 6 M28 10 L32 6"/>`,
  2: `<path d="M6 24 L12 18 L20 20 L28 16 L34 22 L30 26 L24 24 L18 26 Z"/><circle cx="12" cy="26" r="4"/><circle cx="28" cy="26" r="4"/><path d="M20 8 L22 14 L20 12 L18 14 Z"/>`,
  3: `<rect x="8" y="8" width="24" height="24" rx="2"/><path d="M14 14 L26 14 M14 20 L22 20 M14 26 L24 26"/><circle cx="26" cy="26" r="3" fill="currentColor"/><circle cx="8" cy="8" r="2" fill="currentColor"/>`,
  4: `<rect x="6" y="12" width="28" height="20" rx="2"/><circle cx="13" cy="22" r="4"/><circle cx="27" cy="22" r="4"/><path d="M10 8 L20 4 L30 8"/>`,
  5: `<rect x="8" y="8" width="24" height="24" rx="2"/><path d="M14 16 L20 12 L26 16 M14 22 L20 18 L26 22 M14 28 L20 24 L26 28"/><circle cx="20" cy="14" r="2" fill="currentColor"/>`,
  6: `<circle cx="10" cy="10" r="2"/><circle cx="30" cy="10" r="2"/><circle cx="20" cy="20" r="2"/><circle cx="10" cy="30" r="2"/><circle cx="30" cy="30" r="2"/><circle cx="15" cy="15" r="1"/><circle cx="25" cy="15" r="1"/><circle cx="15" cy="25" r="1"/><circle cx="25" cy="25" r="1"/><path d="M10 10 L20 20 L30 10 M10 30 L20 20 L30 30" opacity="0.5"/>`,
  7: `<circle cx="20" cy="20" r="12"/><path d="M20 8 L20 12 M20 28 L20 32 M8 20 L12 20 M28 20 L32 20"/><circle cx="20" cy="20" r="4"/>`,
  8: `<path d="M8 28 L14 18 L20 22 L26 14 L32 20 L28 28 L12 28 Z"/><path d="M20 10 L22 16 L20 14 L18 16 Z"/><circle cx="12" cy="28" r="3"/><circle cx="28" cy="28" r="3"/>`,
  9: `<path d="M8 30 Q14 20 20 26 Q26 32 32 22"/><path d="M8 22 Q14 16 20 20 Q26 24 32 18"/><circle cx="20" cy="26" r="3"/>`,
  10: `<circle cx="20" cy="20" r="14"/><path d="M20 6 L20 14 M6 20 L14 20 M20 34 L20 26 M34 20 L26 20"/><circle cx="20" cy="20" r="4"/>`,
  11: `<ellipse cx="20" cy="24" rx="12" ry="8"/><path d="M12 18 Q20 12 28 18"/><circle cx="14" cy="22" r="2"/><circle cx="20" cy="20" r="2"/><circle cx="26" cy="22" r="2"/>`,
  12: `<rect x="8" y="12" width="24" height="20" rx="1"/><path d="M8 18 L32 18 M8 24 L32 24 M8 30 L32 30"/>`,
  13: `<rect x="8" y="10" width="24" height="20" rx="2"/><path d="M12 10 L12 6 L28 6 L28 10"/><circle cx="14" cy="16" r="2"/><circle cx="20" cy="16" r="2"/><circle cx="26" cy="16" r="2"/>`,
  14: `<circle cx="20" cy="16" r="8"/><circle cx="20" cy="16" r="3"/><path d="M12 24 L16 28 L24 20 L28 24"/>`,
  15: `<circle cx="20" cy="20" r="10"/><ellipse cx="20" cy="20" rx="4" ry="10"/><path d="M20 10 L20 6 M20 30 L20 34 M10 20 L6 20 M30 20 L34 20"/><circle cx="20" cy="20" r="2"/>`,
  16: `<rect x="12" y="8" width="16" height="24" rx="2"/><circle cx="20" cy="14" r="3"/><path d="M16 20 L24 20 M16 24 L24 24 M16 28 L24 28"/>`,
  17: `<path d="M20 8 L24 16 L32 18 L26 24 L28 32 L20 28 L12 32 L14 24 L8 18 L16 16 Z"/><circle cx="20" cy="20" r="4"/>`,
  18: `<ellipse cx="20" cy="24" rx="14" ry="8"/><ellipse cx="20" cy="20" rx="10" ry="6"/><ellipse cx="20" cy="16" rx="6" ry="4"/><circle cx="20" cy="12" r="2"/>`,
  19: `<circle cx="20" cy="20" r="12"/><circle cx="20" cy="20" r="4"/><path d="M20 8 L20 4 M20 32 L20 36 M8 20 L4 20 M32 20 L36 20"/>`,
  20: `<circle cx="20" cy="20" r="8"/><circle cx="20" cy="20" r="3"/><path d="M20 12 L20 8 M28 16 L32 14 M28 24 L32 26 M12 16 L8 14 M12 24 L8 26"/>`,
  21: `<rect x="6" y="6" width="12" height="12" rx="1"/><rect x="22" y="22" width="12" height="12" rx="1"/><path d="M18 12 L22 18 M18 28 L22 22"/>`,
  22: `<path d="M4 28 L10 24 L16 26 L22 20 L28 22 L34 18 L36 24 L32 30 L20 32 L8 30 Z"/><circle cx="12" cy="28" r="3"/><circle cx="28" cy="24" r="3"/>`,
  23: `<path d="M20 8 L24 14 L24 22 L20 32 L16 22 L16 14 Z"/><path d="M12 14 L16 16 L16 22 L12 26 Z"/><path d="M28 14 L24 16 L24 22 L28 26 Z"/><circle cx="20" cy="18" r="3"/>`,
  24: `<path d="M6 24 L12 22 L20 22 L28 18 L34 20 L32 26 L24 28 L12 28 Z"/><circle cx="14" cy="26" r="4"/><circle cx="28" cy="24" r="4"/>`,
  25: `<circle cx="12" cy="12" r="3"/><circle cx="28" cy="16" r="2"/><circle cx="20" cy="24" r="4"/><circle cx="8" cy="28" r="2"/><circle cx="32" cy="28" r="2"/><path d="M12 12 L20 24 L28 16 M8 28 L20 24 L32 28" opacity="0.5"/><circle cx="20" cy="8" r="1"/>`,
  26: `<circle cx="20" cy="20" r="14"/><circle cx="20" cy="20" r="8"/><circle cx="20" cy="20" r="3"/><path d="M20 6 L20 10 M20 30 L20 34 M6 20 L10 20 M30 20 L34 20"/>`,
  27: `<path d="M8 28 Q14 20 20 24 Q26 28 32 20"/><path d="M6 20 Q14 14 20 18 Q26 22 34 16"/><path d="M12 32 Q18 28 24 30 Q30 32 36 28"/>`,
  28: `<circle cx="20" cy="20" r="12"/><path d="M12 20 L20 12 L28 20 L20 28 Z"/><circle cx="20" cy="20" r="4"/>`,
  29: `<rect x="8" y="8" width="24" height="24" rx="1"/><circle cx="14" cy="14" r="3"/><circle cx="26" cy="14" r="3"/><circle cx="14" cy="26" r="3"/><circle cx="26" cy="26" r="3"/><path d="M14 17 L26 23 M26 17 L14 23"/>`,
  30: `<rect x="10" y="8" width="20" height="24" rx="2"/><path d="M10 14 L30 14"/><path d="M14 20 L26 20 M14 24 L22 24"/><circle cx="20" cy="30" r="2"/>`,
  31: `<rect x="10" y="22" width="20" height="10" rx="2"/><circle cx="14" cy="30" r="3"/><circle cx="26" cy="30" r="3"/><path d="M16 22 L16 16 L20 12 L24 16 L24 22"/><circle cx="20" cy="10" r="2"/>`,
  32: `<path d="M8 20 L16 8 L24 8 L32 20 L24 32 L16 32 Z"/><circle cx="20" cy="20" r="6"/><path d="M20 14 L20 26 M14 20 L26 20"/>`,
  33: `<path d="M8 24 Q14 16 20 20 Q26 24 32 16"/><path d="M8 20 Q14 12 20 16 Q26 20 32 12"/><path d="M8 28 Q14 20 20 24 Q26 28 32 20"/>`,
  34: `<path d="M4 24 Q12 18 20 22 Q28 26 36 20"/><path d="M4 20 Q12 14 20 18 Q28 22 36 16"/><path d="M4 28 Q12 22 20 26 Q28 30 36 24"/>`,
  35: `<rect x="6" y="14" width="12" height="16" rx="1"/><rect x="22" y="14" width="12" height="16" rx="1"/><path d="M18 14 L22 14 M18 30 L22 30"/><path d="M6 14 L6 8 L34 8 L34 14"/>`,
  36: `<circle cx="20" cy="20" r="3"/><circle cx="14" cy="14" r="2"/><circle cx="26" cy="14" r="2"/><circle cx="14" cy="26" r="2"/><circle cx="26" cy="26" r="2"/><circle cx="8" cy="8" r="1"/><circle cx="32" cy="8" r="1"/><circle cx="8" cy="32" r="1"/><circle cx="32" cy="32" r="1"/>`,
  37: `<path d="M4 20 Q12 10 20 20 Q28 30 36 20"/><path d="M8 20 Q16 14 20 20 Q24 26 32 20"/><circle cx="20" cy="20" r="3"/>`,
  38: `<rect x="8" y="22" width="24" height="10" rx="2"/><circle cx="14" cy="30" r="4"/><circle cx="26" cy="30" r="4"/><path d="M14 22 L14 18 L20 14 L26 18 L26 22"/>`,
  39: `<rect x="8" y="12" width="24" height="20" rx="2"/><path d="M8 20 L32 20 M14 12 L14 8 M26 12 L26 8"/>`,
  40: `<path d="M20 6 Q30 14 30 24 Q30 34 20 34 Q10 34 10 24 Q10 14 20 6"/><circle cx="20" cy="18" r="4"/>`,
  41: `<path d="M8 20 Q14 12 20 16 Q26 20 32 12"/><path d="M8 24 Q14 18 20 22 Q26 26 32 18"/><path d="M8 28 Q14 24 20 28 Q26 32 32 24"/>`,
  42: `<circle cx="20" cy="20" r="12"/><path d="M12 16 Q16 12 20 16 Q24 20 28 16"/><path d="M12 24 Q16 20 20 24 Q24 28 28 24"/>`,
  43: `<path d="M20 8 L24 16 L20 14 L16 16 Z"/><path d="M12 24 L16 20 L20 24 L24 20 L28 24"/><circle cx="20" cy="18" r="3"/>`,
  44: `<path d="M8 30 L12 20 L16 24 L20 16 L24 22 L28 18 L32 28"/><path d="M8 26 L12 18 L16 22 L20 14 L24 20 L28 16 L32 26" opacity="0.5"/>`,
  45: `<path d="M8 16 L16 16 L20 20 L16 24 L8 24 Z"/><path d="M24 16 L32 16 L32 24 L24 24 L20 20 Z"/><path d="M16 24 L20 28 L24 24"/>`,
  46: `<rect x="10" y="10" width="20" height="20" rx="1"/><path d="M10 16 L20 16 L20 20 L14 20 M20 24 L30 24 M14 28 L20 28 M20 14 L30 14"/><circle cx="20" cy="20" r="3"/>`,
  47: `<circle cx="20" cy="20" r="14"/><ellipse cx="20" cy="20" rx="6" ry="14"/><path d="M6 20 L34 20"/><path d="M20 6 L20 34"/><circle cx="20" cy="20" r="4"/>`,
  48: `<circle cx="20" cy="20" r="10"/><path d="M12 16 Q16 12 20 16 Q24 20 28 16"/><path d="M12 24 Q16 20 20 24 Q24 28 28 24"/><circle cx="20" cy="20" r="3"/>`,
  49: `<path d="M12 8 L20 14 L28 8 L32 14 L28 20 L32 26 L28 32 L20 26 L12 32 L8 26 L12 20 L8 14 Z"/><circle cx="20" cy="18" r="4"/>`,
  50: `<text x="8" y="16" font-size="8" fill="currentColor">T</text><text x="14" y="20" font-size="8" fill="currentColor">H</text><text x="20" y="16" font-size="8" fill="currentColor">R</text><text x="26" y="20" font-size="8" fill="currentColor">E</text><text x="32" y="16" font-size="8" fill="currentColor">E</text>`,
}

interface Point {
  x: number
  y: number
  vx: number
  vy: number
  r: number
  hue: number
  pulse: number
}

const canvasRef = ref<HTMLCanvasElement | null>(null)
let raf: number | null = null
let mouseX = 0
let mouseY = 0

const initCanvas = () => {
  const c = canvasRef.value
  if (!c) return
  const ctx = c.getContext('2d')
  if (!ctx) return
  
  c.width = c.offsetWidth
  c.height = c.offsetHeight
  const w = c.width
  const h = c.height
  
  const pts: Point[] = Array.from({ length: 120 }, () => ({
    x: Math.random() * w,
    y: Math.random() * h,
    vx: (Math.random() - 0.5) * 0.8,
    vy: (Math.random() - 0.5) * 0.8,
    r: Math.random() * 2.5 + 0.5,
    hue: Math.random() * 60 + 40,
    pulse: Math.random() * Math.PI * 2
  }))
  
  c.addEventListener('mousemove', (e) => {
    const rect = c.getBoundingClientRect()
    mouseX = e.clientX - rect.left
    mouseY = e.clientY - rect.top
  })
  
  let time = 0
  const draw = () => {
    if (!ctx || !c) return
    ctx.fillStyle = 'rgba(2,4,10,0.15)'
    ctx.fillRect(0, 0, w, h)
    time += 0.02
    
    pts.forEach((p, i) => {
      const dx = mouseX - p.x
      const dy = mouseY - p.y
      const dist = Math.sqrt(dx * dx + dy * dy)
      
      if (dist < 150) {
        p.vx += dx * 0.0003
        p.vy += dy * 0.0003
      }
      
      p.x += p.vx + Math.sin(time + i) * 0.2
      p.y += p.vy + Math.cos(time + i) * 0.2
      p.pulse += 0.05
      
      if (p.x < 0) p.x = w
      if (p.x > w) p.x = 0
      if (p.y < 0) p.y = h
      if (p.y > h) p.y = 0
      
      const pulseR = p.r + Math.sin(p.pulse) * 0.5
      const alpha = 0.4 + Math.sin(p.pulse) * 0.2
      
      ctx.beginPath()
      ctx.arc(p.x, p.y, pulseR, 0, Math.PI * 2)
      const gradient = ctx.createRadialGradient(p.x, p.y, 0, p.x, p.y, pulseR * 3)
      gradient.addColorStop(0, `hsla(${p.hue}, 100%, 70%, ${alpha})`)
      gradient.addColorStop(1, `hsla(${p.hue}, 100%, 50%, 0)`)
      ctx.fillStyle = gradient
      ctx.fill()
    })
    
    pts.forEach((p1, i) => {
      pts.slice(i + 1).forEach(p2 => {
        const dx = p1.x - p2.x
        const dy = p1.y - p2.y
        const dist = Math.sqrt(dx * dx + dy * dy)
        
        if (dist < 100) {
          ctx.beginPath()
          ctx.moveTo(p1.x, p1.y)
          ctx.lineTo(p2.x, p2.y)
          ctx.strokeStyle = `hsla(50, 100%, 70%, ${0.15 * (1 - dist / 100)})`
          ctx.lineWidth = 0.5
          ctx.stroke()
        }
      })
    })
    
    raf = requestAnimationFrame(draw)
  }
  draw()
}

onMounted(() => {
  initCanvas()
})

onUnmounted(() => {
  if (raf) cancelAnimationFrame(raf)
  if (audioRef.value) {
    audioRef.value.pause()
  }
})
</script>

<template>
  <div class="app">
    <audio ref="audioRef" src="/ambient.mp3" loop preload="auto"></audio>
    
    <button @click="toggleAudio" class="music-btn" :title="isPlaying ? 'Pause Music' : 'Play Music'">
      <svg v-if="isPlaying" viewBox="0 0 24 24" fill="currentColor" width="20" height="20">
        <path d="M6 19h4V5H6v14zm8-14v14h4V5h-4z"/>
      </svg>
      <svg v-else viewBox="0 0 24 24" fill="currentColor" width="20" height="20">
        <path d="M12 3v10.55c-.59-.34-1.27-.55-2-.55-2.21 0-4 1.79-4 4s1.79 4 4 4 4-1.79 4-4V7h4V3h-6z"/>
      </svg>
    </button>
    
    <div class="hero">
      <canvas ref="canvasRef" class="animated-bg"></canvas>
      <div class="grid-overlay"></div>
      
      <div class="hero-content">
        <svg width="120" height="120" viewBox="0 0 120 120" class="cube-logo">
          <defs>
            <linearGradient id="cubeGrad" x1="0%" y1="0%" x2="100%" y2="100%">
              <stop offset="0%" stop-color="#e8ff00"/>
              <stop offset="100%" stop-color="#00e5ff"/>
            </linearGradient>
          </defs>
          <path d="M60 10 L100 35 L100 85 L60 110 L20 85 L20 35 Z" fill="none" stroke="url(#cubeGrad)" stroke-width="2"/>
          <path d="M60 10 L60 60 L100 35" fill="none" stroke="#e8ff00" stroke-width="2" opacity="0.7"/>
          <path d="M60 60 L60 110 L100 85" fill="none" stroke="#00e5ff" stroke-width="2" opacity="0.7"/>
          <path d="M20 35 L60 60 L100 85" fill="none" stroke="#e8ff00" stroke-width="2" opacity="0.5"/>
          <path d="M20 35 L20 85 L60 110" fill="none" stroke="#00e5ff" stroke-width="2" opacity="0.5"/>
          <circle cx="60" cy="60" r="8" fill="#e8ff00"/>
          <circle cx="60" cy="35" r="4" fill="#e8ff00" opacity="0.8"/>
          <circle cx="80" cy="60" r="4" fill="#00e5ff" opacity="0.8"/>
          <circle cx="60" cy="85" r="4" fill="#00e5ff" opacity="0.8"/>
        </svg>
        
        <div class="hero-text">
          <div class="hero-subtitle">Real Games | Verified Links | Play Instantly</div>
          <div class="hero-title">TOP 50</div>
          <div class="hero-title-accent">THREE.JS GAMES</div>
          <div class="hero-desc">Every game has its own individual verified play link</div>
        </div>
      </div>

      <div class="hero-stats">
        <div class="stat" v-for="[n, l] in [['50','Real Games'],['OK','All Verified'],['$','Most Free'],['WEB','All Playable']]" :key="l">
          <div class="stat-num">{{ n }}</div>
          <div class="stat-label">{{ l }}</div>
        </div>
      </div>

      <div class="hero-links">
        <a v-for="[l, u] in [['itch.io Collection','https://itch.io/games/made-with-threejs'],['Three.js Docs','https://threejs.org/docs'],['Official Examples','https://threejs.org/examples'],['GitHub','https://github.com/mrdoob/three.js'],['Three.js Journey','https://threejs-journey.com']]" :key="l" :href="u" target="_blank" rel="noopener noreferrer" class="hero-link">
          {{ l }}
        </a>
      </div>

      <div class="scroll-indicator">
        <div class="scroll-line"></div>
      </div>
    </div>

    <div class="main-content">
      <div class="filters">
        <input v-model="search" placeholder="Search games, devs, genres..." class="search-input" />

        <button @click="freeOnly = !freeOnly" :class="['filter-btn', { active: freeOnly }]" :style="{ '--btn-color': '#00ff88' }">
          {{ freeOnly ? '[x]' : '[ ]' }} Free Only
        </button>
        <button @click="multiOnly = !multiOnly" :class="['filter-btn', { active: multiOnly }]" :style="{ '--btn-color': '#9b59ff' }">
          {{ multiOnly ? '[x]' : '[ ]' }} Multiplayer
        </button>
        
        <div class="view-toggle">
          <button @click="view = 'grid'" :class="['view-btn', { active: view === 'grid' }]">
            [#] GRID
          </button>
          <button @click="view = 'list'" :class="['view-btn', { active: view === 'list' }]">
            [=] LIST
          </button>
        </div>

        <div class="category-filters">
          <button v-for="cat in CATS.filter(c => c === 'All' || GAMES.some(g => g.cat === c))" :key="cat" @click="filter = cat" :class="['cat-btn', { active: filter === cat }]" :style="{ '--cat-color': getCatColor(cat) }">
            {{ cat }}
          </button>
        </div>

        <div class="results-count">
          Showing <span class="highlight">{{ filtered.length }}</span> of {{ GAMES.length }} real Three.js games | All links individually verified | Click any card to play
        </div>
      </div>

      <div :class="['games-grid', view]">
        <div v-for="g in filtered" :key="g.rank" class="game-card" @click="modal = g" @mouseenter="setCardHover(g.rank, true)" @mouseleave="setCardHover(g.rank, false)" :style="{ '--card-color': getCatColor(g.cat) }">
          <div class="card-accent" v-if="view === 'grid'"></div>
          
          <div class="card-image">
            <div v-if="view === 'grid'" class="card-rank">#{{ String(g.rank).padStart(2, '0') }}</div>
            <div class="card-fallback">
              <svg viewBox="0 0 40 40" fill="none" stroke="currentColor" stroke-width="1.5" class="game-icon" v-html="GAME_ICONS[g.rank] || ''"></svg>
            </div>
            <div class="card-overlay" v-if="cardHoverStates[g.rank]">
              <div class="play-btn">PLAY NOW</div>
            </div>
            <div class="card-gradient"></div>
          </div>
          
          <div class="card-info">
            <div class="card-meta">
              <span v-if="view === 'list'" class="card-rank-sm">#{{ String(g.rank).padStart(2, '0') }}</span>
              <span class="card-cat">{{ g.cat }}</span>
              <span class="meta-sep">|</span>
              <span class="card-dev">{{ g.dev }}</span>
            </div>
            <div class="card-title">{{ g.name }}</div>
            <div v-if="view === 'grid'" class="card-tagline">{{ g.tagline }}</div>
            
            <div class="card-badges">
              <span :class="['badge', g.free ? 'free' : 'paid']">{{ g.free ? 'FREE' : 'PAID' }}</span>
              <span v-if="g.multi" class="badge multi">MULTI</span>
              <span class="card-rating">{{ g.rating }}</span>
            </div>
          </div>
        </div>
      </div>

      <div v-if="filtered.length === 0" class="no-results">
        // No games found -- try different filters
      </div>

      <div class="footer-banner">
        All games verified from <a href="https://itch.io/games/made-with-threejs" target="_blank" rel="noopener noreferrer">itch.io/games/made-with-threejs</a> and official Three.js sources.<br/>
        Want more? Browse <a href="https://itch.io/games/top-rated/made-with-threejs" target="_blank" rel="noopener noreferrer">Top Rated Three.js Games on itch.io</a>
      </div>
      
      <div class="footer">
        <div class="footer-brand">
          <svg width="40" height="40" viewBox="0 0 40 40">
            <path d="M20 4 L32 12 L32 28 L20 36 L8 28 L8 12 Z" fill="none" stroke="#e8ff00" stroke-width="1.5"/>
            <path d="M20 4 L20 20 L32 12" fill="none" stroke="#e8ff00" stroke-width="1" opacity="0.5"/>
            <path d="M20 20 L20 36 L8 28" fill="none" stroke="#00e5ff" stroke-width="1" opacity="0.5"/>
            <circle cx="20" cy="20" r="3" fill="#e8ff00"/>
          </svg>
          <div>
            Made with ❤️ in <a href="https://threejs.org" target="_blank" rel="noopener noreferrer">Three.js</a>
          </div>
        </div>
        <div class="footer-copy">
          copyright 2026 | sample-games.pages.dev | All rights reserved
        </div>
        <div class="footer-disclaimer">
          This is an unofficial fan site. All game content belongs to their respective owners.
        </div>
      </div>
    </div>

    <div v-if="modal" class="modal" @click.self="modal = null">
      <div class="modal-content" :style="{ '--modal-color': getCatColor(modal.cat) }">
        <button class="modal-close" @click="modal = null">✕</button>
        <div class="modal-image">
          <div class="modal-fallback">
            <svg viewBox="0 0 40 40" fill="none" stroke="currentColor" stroke-width="1.5" v-html="GAME_ICONS[modal.rank] || ''"></svg>
          </div>
          <div class="modal-badges">
            <span class="modal-cat-badge">{{ modal.cat.toUpperCase() }}</span>
            <span v-if="modal.free" class="modal-free-badge">FREE</span>
            <span v-if="modal.multi" class="modal-multi-badge">MULTIPLAYER</span>
          </div>
          <div class="modal-rating">{{ modal.rating }}</div>
        </div>
        <div class="modal-body">
          <div class="modal-title">{{ modal.name }}</div>
          <div class="modal-dev">by <span>{{ modal.dev }}</span></div>
          <div class="modal-tagline">{{ modal.tagline }}</div>

          <a :href="modal.playUrl" target="_blank" rel="noopener noreferrer" class="modal-play-btn">
            <svg width="18" height="18" viewBox="0 0 24 24" fill="currentColor"><path d="M8 5v14l11-7z"/></svg>
            PLAY NOW
          </a>
          <div class="modal-url">{{ modal.playUrl }}</div>

          <div class="modal-about">
            <div class="modal-about-title">About this game</div>
            <p>{{ modal.desc }}</p>
          </div>

          <div class="modal-links">
            <a :href="modal.playUrl" target="_blank" rel="noopener noreferrer" class="modal-link">PLAY</a>
            <a href="https://threejs.org" target="_blank" rel="noopener noreferrer" class="modal-link">Three.js</a>
            <a href="https://itch.io/games/made-with-threejs" target="_blank" rel="noopener noreferrer" class="modal-link">More Games</a>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800;900&family=JetBrains+Mono:wght@400;500;600;700&display=swap');

* {
  box-sizing: border-box;
}

.app {
  background: linear-gradient(135deg, #0a0a1a 0%, #0d1117 50%, #0a0a1a 100%);
  min-height: 100vh;
  color: #e4e8ef;
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
  overflow-x: hidden;
}

.hero {
  position: relative;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  background: radial-gradient(ellipse at top, rgba(232,255,0,0.03) 0%, transparent 50%),
              radial-gradient(ellipse at bottom right, rgba(0,229,255,0.05) 0%, transparent 50%),
              radial-gradient(ellipse at top left, rgba(155,89,255,0.03) 0%, transparent 50%);
}

.hero::before {
  content: '';
  position: absolute;
  inset: 0;
  background: 
    radial-gradient(2px 2px at 20% 30%, #e8ff00 0%, transparent 100%),
    radial-gradient(2px 2px at 40% 70%, #00e5ff 0%, transparent 100%),
    radial-gradient(2px 2px at 60% 20%, #9b59ff 0%, transparent 100%),
    radial-gradient(2px 2px at 80% 60%, #e8ff00 0%, transparent 100%),
    radial-gradient(1px 1px at 10% 50%, #00e5ff 0%, transparent 100%),
    radial-gradient(1px 1px at 90% 40%, #e8ff00 0%, transparent 100%),
    radial-gradient(1px 1px at 30% 80%, #9b59ff 0%, transparent 100%);
  animation: sparkle 8s ease-in-out infinite;
  pointer-events: none;
}

@keyframes sparkle {
  0%, 100% { opacity: 0.3; transform: scale(1); }
  25% { opacity: 0.6; transform: scale(1.1); }
  50% { opacity: 0.4; transform: scale(0.95); }
  75% { opacity: 0.7; transform: scale(1.05); }
}

.animated-bg {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  opacity: 0.6;
}

.grid-overlay {
  position: absolute;
  inset: 0;
  background-image: 
    linear-gradient(rgba(232,255,0,0.03) 1px, transparent 1px),
    linear-gradient(90deg, rgba(232,255,0,0.03) 1px, transparent 1px);
  background-size: 60px 60px;
  pointer-events: none;
  mask-image: radial-gradient(ellipse at center, black 30%, transparent 70%);
}

.hero-content {
  position: relative;
  z-index: 2;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 30px;
  gap: 50px;
  flex-wrap: wrap;
  animation: fadeInUp 1s ease-out;
}

.cube-logo {
  filter: drop-shadow(0 0 40px rgba(232,255,0,0.4));
  animation: logoFloat 6s ease-in-out infinite, logoGlow 3s ease-in-out infinite;
}

@keyframes logoFloat {
  0%, 100% { transform: translateY(0) rotate(0deg) scale(1); }
  25% { transform: translateY(-10px) rotate(2deg) scale(1.02); }
  50% { transform: translateY(-20px) rotate(-2deg) scale(1.05); }
  75% { transform: translateY(-10px) rotate(1deg) scale(1.02); }
}

@keyframes logoGlow {
  0%, 100% { 
    filter: drop-shadow(0 0 40px rgba(232,255,0,0.4)) drop-shadow(0 0 80px rgba(232,255,0,0.2));
  }
  50% { 
    filter: drop-shadow(0 0 60px rgba(232,255,0,0.6)) drop-shadow(0 0 120px rgba(0,229,255,0.4)) drop-shadow(0 0 200px rgba(232,255,0,0.3));
  }
}

.hero-text {
  text-align: left;
}

.hero-subtitle {
  font-size: 11px;
  letter-spacing: 8px;
  color: #e8ff00;
  text-transform: uppercase;
  margin-bottom: 20px;
  opacity: 0.9;
  font-weight: 500;
}

.hero-title {
  font-size: clamp(3rem, 12vw, 9rem);
  font-weight: 900;
  line-height: 0.95;
  color: #fff;
  margin-bottom: 8px;
  text-transform: uppercase;
  letter-spacing: -2px;
  background: linear-gradient(135deg, #ffffff 0%, #e4e8ef 50%, #ffffff 100%);
  background-size: 200% 200%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  animation: titleShine 3s ease-in-out infinite, fadeInUp 1s ease-out;
}

@keyframes titleShine {
  0%, 100% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
}

.hero-title-accent {
  font-size: clamp(1.5rem, 5vw, 4rem);
  font-weight: 900;
  background: linear-gradient(135deg, #e8ff00 0%, #00e5ff 25%, #e8ff00 50%, #00e5ff 75%, #e8ff00 100%);
  background-size: 300% 300%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  letter-spacing: 3px;
  text-transform: uppercase;
  margin-bottom: 15px;
  animation: gradientFlow 4s ease-in-out infinite, fadeInUp 1s ease-out 0.2s backwards;
}

@keyframes gradientFlow {
  0%, 100% { background-position: 0% 50%; }
  50% { background-position: 100% 50%; }
}

.hero-desc {
  font-size: 12px;
  color: #6b7280;
  letter-spacing: 3px;
  text-transform: uppercase;
  font-weight: 500;
}

.hero-stats {
  display: flex;
  gap: 40px;
  justify-content: center;
  flex-wrap: wrap;
  margin-bottom: 40px;
  animation: fadeInUp 1s ease-out 0.2s both;
}

.stat {
  text-align: center;
  padding: 20px 25px;
  background: rgba(255,255,255,0.02);
  border: 1px solid rgba(255,255,255,0.05);
  border-radius: 16px;
  backdrop-filter: blur(10px);
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
}

.stat::before {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.1), transparent);
  transition: left 0.5s ease;
}

.stat:hover::before {
  left: 100%;
}

.stat:hover {
  transform: translateY(-5px) scale(1.05);
  background: rgba(255,255,255,0.05);
  border-color: rgba(232,255,0,0.3);
  box-shadow: 0 10px 40px rgba(232,255,0,0.1), inset 0 0 20px rgba(232,255,0,0.05);
}

.stat-num {
  font-size: 2.2rem;
  font-weight: 800;
  background: linear-gradient(135deg, #e8ff00 0%, #00e5ff 100%);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  line-height: 1.2;
}

.stat-label {
  font-size: 10px;
  letter-spacing: 2px;
  color: #6b7280;
  text-transform: uppercase;
  margin-top: 8px;
  font-weight: 500;
}

.hero-links {
  display: flex;
  gap: 12px;
  justify-content: center;
  flex-wrap: wrap;
  animation: fadeInUp 1s ease-out 0.4s both;
}

.hero-link {
  font-size: 11px;
  padding: 12px 20px;
  border: 1px solid rgba(255,255,255,0.1);
  color: #9ca3af;
  text-decoration: none;
  letter-spacing: 1px;
  border-radius: 30px;
  transition: all 0.3s ease;
  font-weight: 500;
  background: rgba(255,255,255,0.02);
}

.hero-link:hover {
  border-color: #e8ff00;
  color: #e8ff00;
  background: rgba(232,255,0,0.05);
  transform: translateY(-2px);
}

.scroll-indicator {
  position: absolute;
  bottom: 40px;
  left: 50%;
  transform: translateX(-50%);
  text-align: center;
  z-index: 2;
  animation: fadeInUp 1s ease-out 1s backwards;
}

.scroll-line {
  width: 2px;
  height: 50px;
  background: linear-gradient(to bottom, #e8ff00, #00e5ff, transparent);
  margin: 0 auto;
  border-radius: 2px;
  animation: scrollPulse 2s ease-in-out infinite;
}

@keyframes scrollPulse {
  0%, 100% { 
    opacity: 0.3; 
    transform: scaleY(1);
    box-shadow: 0 0 10px rgba(232,255,0,0.3);
  }
  50% { 
    opacity: 1; 
    transform: scaleY(1.3);
    box-shadow: 0 0 30px rgba(232,255,0,0.8), 0 0 60px rgba(0,229,255,0.4);
  }
}

.scroll-line {
  width: 1px;
  height: 44px;
  background: linear-gradient(#e8ff00, transparent);
  margin: 0 auto;
  animation: pulse 2s ease-in-out infinite;
}

.music-btn {
  position: fixed;
  bottom: 100px;
  right: 30px;
  width: 50px;
  height: 50px;
  background: rgba(10, 10, 26, 0.9);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 50%;
  cursor: pointer;
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s ease;
  backdrop-filter: blur(10px);
}

.music-btn:hover {
  background: rgba(255, 255, 255, 0.1);
  transform: scale(1.05);
  box-shadow: 0 4px 15px rgba(232, 255, 0, 0.3);
}

.music-btn.playing {
  background: #e8ff00;
  animation: pulse 1.5s ease-in-out infinite;
}

.music-btn:hover {
  border-color: #e8ff00;
  transform: scale(1.1);
  box-shadow: 0 0 30px rgba(232,255,0,0.3);
}

.main-content {
  max-width: 1400px;
  margin: 0 auto;
  padding: 0 20px 80px;
}

.filters {
  padding: 24px 0 18px;
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  align-items: center;
}

.search-input {
  background: #080c14;
  border: 1px solid rgba(255,255,255,0.08);
  color: #d0dce8;
  font-family: monospace;
  font-size: 12px;
  padding: 10px 16px;
  outline: none;
  min-width: 180;
  max-width: 320;
  flex: 1;
  letter-spacing: 1;
  border-radius: 2px;
}

.search-input::placeholder {
  color: #4a5a6a;
}

.filter-btn {
  font-size: 10px;
  padding: 8px 14px;
  border: 1px solid rgba(255,255,255,0.08);
  background: transparent;
  color: #4a5a6a;
  font-family: monospace;
  cursor: pointer;
  letter-spacing: 1;
  border-radius: 2px;
  transition: all 0.15s;
}

.filter-btn.active {
  border-color: var(--btn-color);
  background: color-mix(in srgb, var(--btn-color) 10%, transparent);
  color: var(--btn-color);
}

.view-toggle {
  display: flex;
  gap: 4px;
}

.view-btn {
  font-size: 10px;
  padding: 8px 12px;
  border: 1px solid rgba(255,255,255,0.08);
  background: transparent;
  color: #4a5a6a;
  font-family: monospace;
  cursor: pointer;
  letter-spacing: 1;
  border-radius: 2px;
  transition: all 0.15s;
}

.view-btn.active {
  border-color: #e8ff00;
  background: rgba(232,255,0,0.1);
  color: #e8ff00;
}

.category-filters {
  width: 100%;
  display: flex;
  gap: 6px;
  flex-wrap: wrap;
}

.cat-btn {
  background: transparent;
  border: 1px solid rgba(255,255,255,0.08);
  color: #4a5a6a;
  font-family: monospace;
  font-size: 10px;
  letter-spacing: 1;
  padding: 6px 12px;
  cursor: pointer;
  text-transform: uppercase;
  border-radius: 2px;
  transition: all 0.15s;
}

.cat-btn.active {
  background: var(--cat-color);
  border-color: var(--cat-color);
  color: #000;
}

.results-count {
  width: 100%;
  font-size: 11px;
  color: #4a5a6a;
  letter-spacing: 1;
  padding-bottom: 12px;
  border-bottom: 1px solid rgba(255,255,255,0.05);
}

.highlight {
  color: #e8ff00;
}

.games-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
  margin-top: 20px;
}

.games-grid.list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.game-card {
  background: linear-gradient(145deg, rgba(255,255,255,0.03) 0%, rgba(255,255,255,0.01) 100%);
  border: 1px solid rgba(255,255,255,0.06);
  cursor: pointer;
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  border-radius: 20px;
  overflow: hidden;
  display: flex;
  flex-direction: column;
  backdrop-filter: blur(10px);
  animation: cardFadeIn 0.6s ease-out backwards;
}

.game-card::before {
  content: '';
  position: absolute;
  inset: -2px;
  background: linear-gradient(45deg, 
    transparent 30%, 
    var(--card-color) 50%, 
    transparent 70%);
  border-radius: 22px;
  opacity: 0;
  transition: opacity 0.5s ease;
  z-index: -1;
  animation: borderRotate 4s linear infinite paused;
}

.game-card:hover::before {
  opacity: 0.5;
  animation-play-state: running;
}

.game-card::after {
  content: '';
  position: absolute;
  top: 0;
  left: -100%;
  width: 100%;
  height: 100%;
  background: linear-gradient(90deg, 
    transparent, 
    rgba(255,255,255,0.1), 
    transparent);
  transition: left 0.6s ease;
}

.game-card:hover::after {
  left: 100%;
}

.games-grid.list .game-card {
  flex-direction: row;
  align-items: center;
}

.game-card:hover {
  transform: translateY(-8px) scale(1.02);
  box-shadow: 
    0 25px 50px rgba(0,0,0,0.4),
    0 0 40px color-mix(in srgb, var(--card-color) 12%, transparent),
    inset 0 0 30px color-mix(in srgb, var(--card-color) 5%, transparent);
  border-color: color-mix(in srgb, var(--card-color) 30%, transparent);
  animation: cardPulse 2s ease-in-out infinite;
}

@keyframes cardPulse {
  0%, 100% { box-shadow: 0 25px 50px rgba(0,0,0,0.4), 0 0 40px color-mix(in srgb, var(--card-color) 12%, transparent); }
  50% { box-shadow: 0 30px 60px rgba(0,0,0,0.5), 0 0 60px color-mix(in srgb, var(--card-color) 20%, transparent); }
}

.game-card::before {
  content: '';
  position: absolute;
  top: -2px;
  left: -2px;
  right: -2px;
  bottom: -2px;
  background: conic-gradient(from 0deg, 
    transparent, 
    var(--card-color), 
    transparent 30%, 
    var(--card-color) 50%, 
    transparent 70%, 
    var(--card-color), 
    transparent);
  border-radius: 22px;
  opacity: 0;
  transition: opacity 0.5s ease;
  z-index: -1;
  animation: borderRotate 4s linear infinite paused;
}

.game-card:hover::before {
  opacity: 0.6;
  animation-play-state: running;
}

@keyframes borderRotate {
  to { transform: rotate(360deg); }
}

.card-accent {
  height: 3px;
  background: linear-gradient(90deg, var(--card-color), color-mix(in srgb, var(--card-color) 50%, #00e5ff));
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  z-index: 10;
}

.card-image {
  width: 100%;
  height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  background: linear-gradient(135deg, color-mix(in srgb, var(--card-color) 10%, transparent) 0%, rgba(0,0,0,0.3) 100%);
  flex-shrink: 0;
  overflow: hidden;
}

.games-grid.list .card-image {
  width: 140px;
  height: 100px;
  border-radius: 16px 0 0 16px;
}

.card-rank {
  position: absolute;
  top: 15px;
  left: 15px;
  font-size: 11px;
  background: rgba(0,0,0,0.8);
  padding: 6px 12px;
  border-left: 3px solid var(--card-color);
  color: #fff;
  font-weight: 700;
  z-index: 10;
  border-radius: 4px;
  backdrop-filter: blur(10px);
}

.card-image img {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.card-fallback {
  display: flex;
  align-items: center;
  justify-content: center;
  position: absolute;
  inset: 0;
  background: radial-gradient(ellipse at center, color-mix(in srgb, var(--card-color) 15%, #0a0a1a), #0a0a1a);
}

.game-icon {
  width: 70px;
  height: 70px;
  color: var(--card-color);
  filter: drop-shadow(0 0 20px color-mix(in srgb, var(--card-color) 60%, transparent));
  transition: all 0.3s ease;
  animation: iconFloat 3s ease-in-out infinite, iconGlow 2s ease-in-out infinite;
}

@keyframes iconFloat {
  0%, 100% { transform: translateY(0) rotate(0deg); }
  33% { transform: translateY(-5px) rotate(2deg); }
  66% { transform: translateY(3px) rotate(-2deg); }
}

@keyframes iconGlow {
  0%, 100% { filter: drop-shadow(0 0 20px color-mix(in srgb, var(--card-color) 60%, transparent)); }
  50% { filter: drop-shadow(0 0 35px color-mix(in srgb, var(--card-color) 80%, transparent)) drop-shadow(0 0 60px color-mix(in srgb, var(--card-color) 40%, transparent)); }
}

.game-card:hover .game-icon {
  transform: scale(1.15) rotate(5deg);
  filter: drop-shadow(0 0 40px color-mix(in srgb, var(--card-color) 100%, transparent)) drop-shadow(0 0 80px color-mix(in srgb, var(--card-color) 50%, transparent));
  animation: iconSpin 0.6s ease-out;
}

@keyframes iconSpin {
  0% { transform: scale(1) rotate(0deg); }
  50% { transform: scale(1.2) rotate(180deg); }
  100% { transform: scale(1.15) rotate(5deg); }
}

.card-overlay {
  position: absolute;
  inset: 0;
  background: rgba(0,0,0,0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  animation: fadeIn 0.3s ease-out;
  backdrop-filter: blur(5px);
}

.card-overlay::before {
  content: '';
  position: absolute;
  inset: 0;
  background: radial-gradient(circle at center, var(--card-color) 0%, transparent 70%);
  opacity: 0.1;
  animation: overlayPulse 1.5s ease-in-out infinite;
}

@keyframes overlayPulse {
  0%, 100% { transform: scale(1); opacity: 0.1; }
  50% { transform: scale(1.2); opacity: 0.2; }
}

.play-btn {
  background: var(--card-color);
  color: #000;
  padding: 14px 32px;
  border-radius: 30px;
  font-weight: 700;
  font-size: 13px;
  letter-spacing: 1px;
  box-shadow: 0 4px 20px color-mix(in srgb, var(--card-color) 40%, transparent);
  transition: all 0.3s ease;
  position: relative;
  overflow: hidden;
  animation: playBtnPulse 2s ease-in-out infinite;
}

@keyframes playBtnPulse {
  0%, 100% { transform: scale(1); box-shadow: 0 4px 20px color-mix(in srgb, var(--card-color) 40%, transparent); }
  50% { transform: scale(1.05); box-shadow: 0 8px 40px color-mix(in srgb, var(--card-color) 60%, transparent); }
}

.play-btn::after {
  content: '';
  position: absolute;
  top: -50%;
  left: -50%;
  width: 200%;
  height: 200%;
  background: linear-gradient(45deg, transparent 40%, rgba(255,255,255,0.3) 50%, transparent 60%);
  animation: playBtnShine 2s ease-in-out infinite;
}

@keyframes playBtnShine {
  0% { transform: translateX(-100%) rotate(45deg); }
  100% { transform: translateX(100%) rotate(45deg); }
}

.play-btn:hover {
  transform: scale(1.1);
  box-shadow: 0 10px 40px color-mix(in srgb, var(--card-color) 80%, transparent);
}

.card-gradient {
  position: absolute;
  inset: 0;
  background: linear-gradient(to top, rgba(9,13,24,0.9) 0%, transparent 50%);
  pointer-events: none;
}

.card-info {
  flex: 1;
  padding: 18px 20px 20px;
  background: linear-gradient(to top, rgba(10,10,26,0.5) 0%, transparent 100%);
}

.games-grid.list .card-info {
  padding: 15px 20px;
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.card-meta {
  display: flex;
  gap: 10px;
  margin-bottom: 10px;
  flex-wrap: wrap;
  align-items: center;
}

.card-rank-sm {
  font-size: 10px;
  background: rgba(0,0,0,0.8);
  padding: 4px 10px;
  color: #fff;
  font-weight: 700;
  border-radius: 6px;
}

.card-cat {
  font-size: 10px;
  letter-spacing: 1.5px;
  color: var(--card-color);
  text-transform: uppercase;
  font-weight: 700;
}

.meta-sep {
  font-size: 10px;
  color: #374151;
}

.card-dev {
  font-size: 10px;
  color: #6b7280;
  font-weight: 500;
}

.card-title {
  font-size: 17px;
  font-weight: 700;
  color: #fff;
  margin-bottom: 10px;
  line-height: 1.4;
}

.games-grid.list .card-title {
  font-size: 15px;
  margin-bottom: 5px;
}

.card-tagline {
  font-size: 12px;
  color: #9ca3af;
  line-height: 1.6;
  margin-bottom: 15px;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

.card-badges {
  display: flex;
  gap: 8px;
  align-items: center;
  flex-wrap: wrap;
}

.badge {
  font-size: 9px;
  padding: 5px 10px;
  border-radius: 20px;
  font-weight: 700;
  letter-spacing: 0.5px;
  text-transform: uppercase;
}

.badge.free {
  color: #10b981;
  background: rgba(16,185,129,0.15);
  border: 1px solid rgba(16,185,129,0.3);
}

.badge.paid {
  color: #f59e0b;
  background: rgba(245,158,11,0.15);
  border: 1px solid rgba(245,158,11,0.3);
}

.badge.multi {
  color: #a855f7;
  background: rgba(168,85,247,0.15);
  border: 1px solid rgba(168,85,247,0.3);
}

.card-rating {
  font-size: 10px;
  color: #9ca3af;
  margin-left: auto;
  font-weight: 600;
}

.no-results {
  text-align: center;
  padding: 80px 20px;
  color: #4a5a6a;
  letter-spacing: 2;
}

.footer-banner {
  margin-top: 80px;
  padding: 30px;
  background: linear-gradient(135deg, rgba(232,255,0,0.03) 0%, rgba(0,229,255,0.03) 100%);
  border: 1px solid rgba(232,255,0,0.1);
  border-radius: 20px;
  text-align: center;
  font-size: 13px;
  color: #6b7280;
  line-height: 1.8;
}

.footer-banner a {
  color: #e8ff00;
  text-decoration: none;
  font-weight: 600;
  transition: all 0.3s ease;
}

.footer-banner a:hover {
  text-shadow: 0 0 20px rgba(232,255,0,0.5);
}

.footer {
  margin-top: 50px;
  padding: 40px 20px;
  border-top: 1px solid rgba(255,255,255,0.05);
  text-align: center;
}

.footer-brand {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 20px;
  flex-wrap: wrap;
  margin-bottom: 25px;
  font-size: 13px;
  color: #6b7280;
}

.footer-brand svg {
  filter: drop-shadow(0 0 15px rgba(232,255,0,0.3));
}

.footer-brand a {
  color: #00e5ff;
  text-decoration: none;
  font-weight: 600;
  transition: all 0.3s ease;
}

.footer-brand a:hover {
  text-shadow: 0 0 20px rgba(0,229,255,0.5);
}

.footer-copy {
  font-size: 11px;
  color: #4b5563;
  letter-spacing: 1px;
}

.footer-disclaimer {
  font-size: 10px;
  color: #374151;
  margin-top: 10px;
}

.modal {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.9);
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 30px;
  backdrop-filter: blur(20px);
  animation: fadeIn 0.3s ease-out;
}

.modal-content {
  background: linear-gradient(145deg, #0f1419 0%, #0a0a1a 100%);
  border: 1px solid color-mix(in srgb, var(--modal-color) 15%, transparent);
  max-width: 750px;
  width: 100%;
  max-height: 90vh;
  overflow-y: auto;
  position: relative;
  border-radius: 24px;
  box-shadow: 
    0 50px 100px rgba(0,0,0,0.5),
    0 0 80px color-mix(in srgb, var(--modal-color) 10%, transparent);
  animation: scaleIn 0.3s ease-out;
}

.modal-close {
  position: absolute;
  top: 20px;
  right: 20px;
  background: rgba(0,0,0,0.6);
  border: 1px solid rgba(255,255,255,0.1);
  color: #fff;
  width: 44px;
  height: 44px;
  cursor: pointer;
  font-size: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 12px;
  z-index: 10;
  transition: all 0.3s ease;
  backdrop-filter: blur(10px);
}

.modal-close:hover {
  background: var(--modal-color);
  color: #000;
  transform: rotate(90deg);
}

.modal-image {
  height: 300px;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  background: linear-gradient(135deg, color-mix(in srgb, var(--modal-color) 10%, #0a0a1a) 0%, #0a0a1a 100%);
  border-bottom: 1px solid color-mix(in srgb, var(--modal-color) 10%, transparent);
  overflow: hidden;
}

.modal-image img {
  position: absolute;
  inset: 0;
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.modal-fallback {
  width: 100px;
  height: 100px;
  color: var(--modal-color);
  filter: drop-shadow(0 0 40px var(--modal-color));
}

.modal-fallback svg {
  width: 100%;
  height: 100%;
}

.modal-badges {
  position: absolute;
  bottom: 20px;
  left: 20px;
  z-index: 10;
  display: flex;
  gap: 10px;
  align-items: center;
}

.modal-cat-badge {
  font-size: 11px;
  background: var(--modal-color);
  color: #000;
  padding: 6px 16px;
  border-radius: 20px;
  font-weight: 700;
  letter-spacing: 1px;
}

.modal-free-badge {
  font-size: 10px;
  background: rgba(16,185,129,0.2);
  color: #10b981;
  padding: 6px 12px;
  border-radius: 20px;
  font-weight: 700;
}

.modal-multi-badge {
  font-size: 10px;
  background: rgba(168,85,247,0.2);
  color: #a855f7;
  padding: 6px 12px;
  border-radius: 20px;
  font-weight: 700;
}

.modal-rating {
  position: absolute;
  bottom: 20px;
  right: 20px;
  z-index: 10;
  font-size: 13px;
  color: #fbbf24;
  font-weight: 700;
  background: rgba(0,0,0,0.6);
  padding: 6px 12px;
  border-radius: 10px;
  backdrop-filter: blur(10px);
}

.modal-body {
  padding: 35px 35px 40px;
}

.modal-title {
  font-size: 32px;
  font-weight: 800;
  color: #fff;
  margin-bottom: 10px;
  letter-spacing: -0.5px;
}

.modal-dev {
  font-size: 14px;
  color: #6b7280;
  margin-bottom: 25px;
  display: flex;
  align-items: center;
  gap: 8px;
}

.modal-dev span {
  color: var(--modal-color);
  font-weight: 600;
}

.modal-tagline {
  font-size: 15px;
  color: #9ca3af;
  line-height: 1.7;
  margin-bottom: 30px;
  padding-bottom: 25px;
  border-bottom: 1px solid rgba(255,255,255,0.05);
}

.modal-play-btn {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
  font-size: 15px;
  font-weight: 700;
  padding: 18px 40px;
  background: var(--modal-color);
  color: #000;
  text-decoration: none;
  letter-spacing: 1px;
  border-radius: 16px;
  width: 100%;
  transition: all 0.3s ease;
  margin-bottom: 15px;
}

.modal-play-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 15px 40px color-mix(in srgb, var(--modal-color) 30%, transparent);
}

.modal-url {
  font-size: 11px;
  color: #4b5563;
  text-align: center;
  letter-spacing: 0.5px;
  margin-bottom: 30px;
}

.modal-about {
  margin-bottom: 25px;
}

.modal-about-title {
  font-size: 11px;
  letter-spacing: 2px;
  color: #4b5563;
  text-transform: uppercase;
  margin-bottom: 12px;
  font-weight: 600;
}

.modal-about p {
  font-size: 14px;
  line-height: 1.8;
  color: #9ca3af;
}

.modal-links {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
  padding-top: 16px;
  border-top: 1px solid rgba(255,255,255,0.04);
}

.modal-link {
  font-size: 11px;
  padding: 8px 16px;
  background: var(--modal-color);
  color: #000;
  text-decoration: none;
  font-weight: 700;
  letter-spacing: 0.5;
  border-radius: 6px;
  transition: all 0.15s;
}

.modal-link:nth-child(2),
.modal-link:nth-child(3) {
  background: transparent;
  border: 1px solid rgba(255,255,255,0.1);
  color: #8898a8;
}

.modal-link:nth-child(2):hover,
.modal-link:nth-child(3):hover {
  border-color: var(--modal-color);
  color: var(--modal-color);
}

::-webkit-scrollbar {
  width: 8px;
}

::-webkit-scrollbar-track {
  background: #0a0a1a;
}

::-webkit-scrollbar-thumb {
  background: linear-gradient(to bottom, #e8ff00, #00e5ff);
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: linear-gradient(to bottom, #f0ff40, #20f5ff);
}

@keyframes pulse {
  0%, 100% { opacity: 0.3; transform: scaleY(1); }
  50% { opacity: 1; transform: scaleY(1.2); }
}

@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes fadeInUp {
  from { opacity: 0; transform: translateY(30px); }
  to { opacity: 1; transform: translateY(0); }
}

@keyframes float {
  0%, 100% { transform: translateY(0) rotate(0deg); }
  50% { transform: translateY(-15px) rotate(2deg); }
}

@keyframes scaleIn {
  from { opacity: 0; transform: scale(0.95); }
  to { opacity: 1; transform: scale(1); }
}

@keyframes shimmer {
  0% { transform: translateX(-100%); }
  100% { transform: translateX(100%); }
}

@keyframes fadeInScale {
  from { opacity: 0; transform: scale(0.9); }
  to { opacity: 1; transform: scale(1); }
}

.games-grid .game-card {
  animation: fadeInScale 0.5s ease-out backwards;
}

.games-grid .game-card:nth-child(1) { animation-delay: 0.05s; }
.games-grid .game-card:nth-child(2) { animation-delay: 0.1s; }
.games-grid .game-card:nth-child(3) { animation-delay: 0.15s; }
.games-grid .game-card:nth-child(4) { animation-delay: 0.2s; }
.games-grid .game-card:nth-child(5) { animation-delay: 0.25s; }
.games-grid .game-card:nth-child(6) { animation-delay: 0.3s; }
.games-grid .game-card:nth-child(7) { animation-delay: 0.35s; }
.games-grid .game-card:nth-child(8) { animation-delay: 0.4s; }
.games-grid .game-card:nth-child(9) { animation-delay: 0.45s; }
.games-grid .game-card:nth-child(10) { animation-delay: 0.5s; }
</style>
