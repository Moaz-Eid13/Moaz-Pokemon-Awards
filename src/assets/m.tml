import * as THREE from 'three'
import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js'

function createScene(containerId, modelPath) {
  const container = document.getElementById(containerId)
  if (!container) return

  const renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true })
  renderer.setSize(container.clientWidth, container.clientHeight)
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
  container.appendChild(renderer.domElement)

  const scene = new THREE.Scene()
  const camera = new THREE.PerspectiveCamera(50, container.clientWidth / container.clientHeight, 0.1, 100)
  camera.position.z = 3

  scene.add(new THREE.AmbientLight(0xffffff, 0.8))
  const dirLight = new THREE.DirectionalLight(0xffffff, 1)
  dirLight.position.set(5, 5, 5)
  scene.add(dirLight)

  let pivot = new THREE.Group()
  scene.add(pivot)

  const loader = new GLTFLoader()
  loader.load(
    modelPath,
    (gltf) => {
      const model = gltf.scene
      model.position.y = -0.5
      pivot.add(model)
    },
    undefined,
    (err) => console.error(`Failed to load ${modelPath}:`, err)
  )

  const idleSpeed = 0.06        
  const dragStrength = 0.015    
  const friction = 0.95         
  
  let velX = idleSpeed
  let velY = 0
  let isDragging = false
  let prevX = 0, prevY = 0

  let targetZoom = camera.position.z, minZoom = 1.0, maxZoom = 8.0
  window.addEventListener('resize', () => {
    camera.aspect = container.clientWidth / container.clientHeight
    camera.updateProjectionMatrix()
    renderer.setSize(container.clientWidth, container.clientHeight)
  })

  const canvas = renderer.domElement
  canvas.addEventListener('wheel', (e) => {
    e.preventDefault()
    targetZoom = Math.max(minZoom, Math.min(maxZoom, targetZoom + e.deltaY * 0.005))
  }, { passive: false })

  canvas.addEventListener('mousedown', (e) => {
    isDragging = true
    prevX = e.clientX
    prevY = e.clientY
    velX = velY = 0
  })

  window.addEventListener('mousemove', (e) => {
    if (!isDragging) return
    velX = (e.clientX - prevX) * dragStrength
    velY = (e.clientY - prevY) * dragStrength
    prevX = e.clientX
    prevY = e.clientY
  })

  window.addEventListener('mouseup', () => {
    isDragging = false
  })

  function animate() {
    requestAnimationFrame(animate)

    camera.position.z += (targetZoom - camera.position.z) * 0.1

    if (!isDragging) {
      velX *= friction
      velY *= friction
      
      if (Math.abs(velX) < 0.001 && Math.abs(velY) < 0.001) {
        velX += (idleSpeed - velX) * 0.02
      }
    }

    const qY = new THREE.Quaternion().setFromAxisAngle(new THREE.Vector3(0, 1, 0), velX)
    const qX = new THREE.Quaternion().setFromAxisAngle(new THREE.Vector3(1, 0, 0), velY)
    pivot.quaternion.premultiply(qY).premultiply(qX)

    renderer.render(scene, camera)
  }

  animate()
}

;['canvas1', 'canvas2', 'canvas3', 'canvas4'].forEach((id, i) => {
  const models = ['Mudkip', 'Seedot', 'Beautifly', 'Glalie']
  createScene(id, `./models/${models[i]}.glb`)
})
